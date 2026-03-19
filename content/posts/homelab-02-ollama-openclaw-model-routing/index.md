---
title: "Which Local Model for Which Task? A Practical Guide to OpenClaw Subagent Routing"
subtitle: "Not all Ollama models handle tool calling. Here's how to route tasks across local models — and when to fall back to cloud."
date: 2026-03-19
series: ["homelab"]
series_order: 2
tags: ["homelab", "AI", "ollama", "openclaw", "self-hosted", "local-AI"]
categories: ["The Homelab"]
draft: true
description: "Running Ollama with OpenClaw means choosing the right model for each task type. This is a practical guide to subagent routing — what works, what fails, and how to structure your config so overnight tasks run free."
---

> **TL;DR:** For OpenClaw subagents, model choice matters more than you think. Small models (<14B) fail at tool calling. Route heartbeats and file ops to cheap local models, and reserve your 27B+ model for anything needing `web_search` or complex reasoning. Cloud models only for the things that genuinely need them.

---

## The Problem With "Just Run It Locally"

Once you've got [Ollama running on your homelab hardware](/posts/homelab-01-ollama-vulkan-amd-proxmox/) and [sorted out the OpenClaw config bugs](/posts/homelab-03-ollama-openclaw-config/), you hit the next question: *which model do you actually use for what?*

For a simple chat interface, this barely matters — response quality scales roughly with parameter count and almost any modern model works. But OpenClaw is an agentic framework. It spawns subagents to do things autonomously: research tasks overnight, file operations, code generation, web searches. In that context, model choice matters a lot, because **tool calling reliability varies enormously across model sizes**.

A model that can't reliably structure a tool call JSON is worse than useless. It either fails silently, or — worse — hallucinates that it ran the tool and returns fabricated results. You don't know the output is wrong until you check.

This post is a practical guide to routing tasks across local Ollama models, based on real testing with OpenClaw subagents.

---

## The Core Split: Tool Calling vs. Text Generation

Before getting into specific models, it helps to understand why this split exists.

When an agent needs to call a tool — say, `web_search` or `exec` — the model has to produce a correctly structured JSON object in a specific format that the framework can parse. This isn't just "write some JSON" — it requires the model to understand the tool schema, select the right tool for the job, fill in the parameters correctly, and produce output that the framework can deserialise.

Community consensus (backed by [testing documented on haimaker.ai](https://haimaker.ai/blog/best-local-models-for-openclaw)) is that **models under roughly 14B parameters are unreliable for this in agentic frameworks**. They either skip the tool call, produce malformed JSON, or attempt to construct the call in a slightly wrong format. The failure mode is often silent — you don't get an error, you just get wrong output.

This creates a natural split in how you route tasks:

| Task type | Needs tools? | Model tier |
|---|---|---|
| Heartbeats, greetings | No | Any small model |
| Summarise a file | Read only | Mid-tier (9B+) |
| Code generation | No (or simple) | Specialised coder model |
| File ops (`exec`, `read`) | Yes (simple) | Mid-tier (8B+) |
| Web research, multi-step | Yes (complex) | Large (27B+) |
| Strategic reasoning, writing | Maybe | Cloud |

---

## The Models and What They're Good For

Here's the lineup I'm running on a MINISFORUM UM790 Pro (Ryzen 9 7940HS, 64GB RAM, Radeon 780M):

### phi3:mini — Microsoft, 3.8B

**Use for:** Heartbeats, periodic checks, simple acknowledgements — anything where the model just needs to respond with text and no tools are involved.

Fast, very low memory footprint, and genuinely good at following simple instructions. Falls apart the moment you ask it to call a tool. Don't use it for anything requiring `web_search`, `exec`, or file operations.

```json
{ "id": "phi3:mini", "reasoning": false, "contextWindow": 4096, "maxTokens": 512 }
```

### llama3.1:8b — Meta, 8B

**Use for:** Basic file operations and workspace tasks. It passed a straightforward `exec` test (listing files) reliably in my testing.

Don't expect complex chained tool calls. Good for "read this file and write a summary to another file" type tasks where the tool interaction is simple and there's only one step. Community reports and [openclaw/openclaw#41871](https://github.com/openclaw/openclaw/issues/41871) confirm it hangs in more complex subagent sessions.

```json
{ "id": "llama3.1:8b", "reasoning": false, "contextWindow": 32768, "maxTokens": 2048 }
```

### qwen2.5-coder:7b — Alibaba, 7B

**Use for:** Code generation tasks specifically. This model has been fine-tuned on code and produces noticeably cleaner output for programming tasks than a general-purpose 7B model.

Don't use it as a general agent. It's a specialist — point it at code tasks and it does well; ask it to browse the web and it won't.

```json
{ "id": "qwen2.5-coder:7b", "reasoning": false, "contextWindow": 32768, "maxTokens": 2048 }
```

### qwen3.5:9b — Alibaba, 9B

**Use for:** Research summaries, reading and digesting files, structured text tasks. In testing it produced an accurate, well-structured summary of a long profile document — capturing the right priorities without hallucinating.

Borderline for tool calling — simple reads work, complex chained searches may not. Use it for tasks where "read and summarise" is the primary action.

```json
{ "id": "qwen3.5:9b", "reasoning": false, "contextWindow": 32768, "maxTokens": 2048 }
```

### qwen3.5:27b-q4_K_M — Alibaba, 27B (Q4 quantised)

**Use for:** Anything requiring reliable tool calling. Overnight research tasks, multi-step `web_search` chains, tasks that need to read context, reason about it, and then call tools based on that reasoning.

In live testing, this model:
1. Read a long context file correctly
2. Identified the top priorities from it
3. Made **three separate `web_search` calls** — all returning real results, not hallucinations
4. Incorporated the search results into a coherent response

That's the bar for "reliable tool calling." It cleared it. The 7B and 8B models did not.

The trade-off: it's slow. On CPU inference (GPU acceleration is still a work in progress on my Proxmox setup), it's noticeably slower than the smaller models. For interactive use this is marginal. For overnight batch tasks, it doesn't matter.

```json
{ "id": "qwen3.5:27b-q4_K_M", "reasoning": false, "contextWindow": 32768, "maxTokens": 4096 }
```

---

## The Routing Config

Here's the full routing I use in practice:

```
Heartbeats / periodic checks     →  phi3:mini              free, fast
Simple file writes                →  llama3.1:8b            free, no tool chains
Code generation                   →  qwen2.5-coder:7b       free, specialist
Summaries / file digests          →  qwen3.5:9b             free, reliable
Overnight research + web search   →  qwen3.5:27b-q4_K_M     free, tool calling works
Complex reasoning / strategy      →  claude-sonnet-4-6      paid, quality gap real
Deep academic writing             →  claude-opus            paid, ask first
```

The goal: push as much as possible to free local inference. With this config, cloud API calls are reserved for the roughly 10-20% of tasks where the quality difference actually matters — strategic reasoning, complex long-form writing, anything where you'd notice the gap.

---

## When to Use Cloud Models Anyway

Being honest about this: for some tasks, local models aren't there yet.

**Complex multi-step reasoning with context** — if you need the model to hold a lot of context in mind while reasoning across multiple steps, a 27B quantised model will occasionally lose the thread in ways a frontier model won't.

**Long-form structured writing** — academic-quality writing, nuanced analysis, anything where you'd proof-read carefully. The quality gap between a 27B local model and Claude Sonnet is real and noticeable.

**Tasks where a wrong answer is costly** — if a subagent is going to take an action based on its output (sending a message, committing code, updating a document), the cost of a hallucination is higher. For high-stakes actions, the cloud models are more reliable.

The practical test: would you proofread the output before using it? If yes, use the best local model that can handle the task type. If you'd use the output directly without checking, use a cloud model.

---

## What's Next

- **Validate qwen3.5:27b as a subagent** — tonight's tests were in the main session. Tool calling reliability in isolated subagent sessions is still unconfirmed.
- **Ollama's native web search API** — Ollama recently added built-in web search. If that works from subagent sessions, it could unlock tool calling for smaller models and cut the need for the 27B.
- **Explore larger models** — 70B+ models via GGUF quantisation. The UM790 Pro has 64GB RAM, so larger quants are theoretically possible.

---

## Key Takeaways

- **Sub-14B models are unreliable for tool calling** in OpenClaw subagents. Use them for text-only tasks.
- **qwen3.5:27b-q4_K_M** is the sweet spot for free local tool calling right now. It's slow but works.
- **Route by task type, not just model size.** A specialised 7B coder model beats a general 9B for code tasks.
- **Cloud models still have a role** — just a smaller one than before.

{{< alert icon="circle-info" cardColor="#f1f5f9" iconColor="#94a3b8" textColor="#475569" >}}
**AI Assistance:** This post was researched and drafted with the assistance of Claude Sonnet 4.6 (Anthropic) via [OpenClaw](https://openclaw.ai). The test results, routing decisions, and editorial judgements are the author's own.
{{< /alert >}}
