---
title: "Which Local Model for Which Task? Testing Ollama Models with OpenClaw"
subtitle: "Not all Ollama models handle tool calling. Here's what I found after running live tests across five models — and how I rewired my AI agent routing as a result."
date: 2026-03-19
series: ["homelab"]
series_order: 2
tags: ["homelab", "ollama", "AI", "LLM", "OpenClaw", "self-hosting", "local-AI", "model-routing"]
categories: ["The Homelab"]
draft: true
description: "I updated my OpenClaw config to include two new Qwen3.5 models and ran live tests across all five local Ollama models. The results were surprising — and changed how I route tasks between local and cloud AI."
---

## The Problem With "Just Use a Local Model"

Once you've got Ollama running with GPU acceleration (see [Part 1](/posts/homelab-01-ollama-vulkan-amd-proxmox)), the next question is deceptively simple: *which model do you actually use for what?*

The naive answer is "the biggest one that fits in VRAM." But that ignores something important: not all models handle tool calling equally well. And if you're running an AI agent framework like [OpenClaw](https://openclaw.ai) — where the models need to call tools like `web_search`, `exec`, and `read` — a model that can't reliably structure a tool call JSON is worse than useless. It either fails silently or, worse, hallucinates that it ran the tool and returns fake results.

I found this out the hard way a few weeks ago. Tonight I updated my config with two new Qwen3.5 models and ran a structured test to find out exactly which models I can actually rely on for which tasks.

Here's what I found.

---

## My Setup

Quick recap for context:

- **Ollama host:** MINISFORUM UM790 Pro — Ryzen 9 7940HS, AMD Radeon 780M (RDNA3), 32GB RAM
- **GPU acceleration:** Vulkan via Mesa/RADV (ROCm doesn't work on Proxmox's patched kernel — full story in Part 1)
- **OpenClaw:** Running on a separate LXC container (MeLE Quieter 4C, 192.168.4.100), connecting to Ollama over the local network
- **Models tested:** phi3:mini, llama3.1:8b, qwen2.5-coder:7b, qwen3.5:9b, qwen3.5:27b-q4_K_M

The two Qwen3.5 models were new additions tonight — I'd been running the first three for a while and already had a rough sense of their capabilities. The 27b model in particular was the one I most wanted to test, because it's the largest that still fits comfortably in my VRAM budget.

---

## The Tests

I ran five tests, each designed to probe something specific:

**Test 1 — phi3:mini:** Switch to the model, say hello.
**Test 2 — qwen2.5-coder:7b:** Write a simple Python hello world function.
**Test 3 — qwen3.5:9b:** Read my USER.md file and summarise it.
**Test 4 — llama3.1:8b:** List the files in my workspace using `exec`.
**Test 5 — qwen3.5:27b-q4_K_M:** Read USER.md, identify my top 3 strategic priorities, suggest one concrete action for each, and use `web_search` to find one relevant resource per priority.

Test 5 is the real test. It requires chaining multiple tools — reading a file, reasoning about its content, and then making three real web search calls. If a model can do that reliably, it's genuinely useful for overnight autonomous research tasks.

---

## Results

| Model | Responded | Tools | Quality | Speed |
|---|---|---|---|---|
| phi3:mini | ✅ | N/A | 3/5 | Fast |
| qwen2.5-coder:7b | ✅ | N/A | 4/5 | Fast |
| qwen3.5:9b | ✅ | ✅ | 4/5 | Medium |
| llama3.1:8b | ✅ | ✅ | 3/5 | Medium |
| qwen3.5:27b-q4_K_M | ✅ | ✅ | 5/5 | Slow |

Every model responded. That's the baseline. But the interesting part is the tool calling column.

### The Small Models

**phi3:mini** and **qwen2.5-coder:7b** both did exactly what they were asked — a greeting and a code snippet respectively — cleanly and quickly. No complaints. But neither was asked to use tools, so this just confirms they're functional.

**llama3.1:8b** used `exec` to list files correctly. Simple tool call, worked fine. But I've tested it more extensively before, and here's the honest assessment: llama3.1:8b is unreliable for anything requiring *structured* tool output. It'll often try to construct the JSON itself rather than using the framework's tool interface, and when it does that, things go sideways. Good for simple file writes with clear instructions. Not good for anything more complex.

### qwen3.5:9b — A Solid Middle Tier

Qwen3.5:9b read my USER.md file and summarised it accurately. It captured the key priorities correctly, kept the summary concise, and didn't hallucinate anything. For tasks that involve reading files and producing structured summaries — think "read these three research papers and give me the key findings" — this model is genuinely reliable and free to run.

### qwen3.5:27b — The Surprise

This is the one I was most curious about, and it delivered. The model:

1. Read my USER.md correctly
2. Correctly identified my top three strategic priorities (MIT application, Dominion Cyber launch, career transition to AI leadership)
3. Suggested a concrete action for each
4. Made **three separate `web_search` calls** — and all three returned real results

That last point is significant. Previous testing had shown me that smaller Ollama models (7b-ish) either fail to structure tool calls correctly or, in the worst case, pretend they ran the search and return fabricated results. The 27b model didn't do either of those things. It formed the tool call correctly, waited for the real result, and incorporated it into its response.

For context, the searches returned:
- MDPI Applied Sciences special issue on acoustic metamaterials (relevant to my research)
- cyber.gov.au small business cybersecurity guide Jan 2025 (relevant to my business)
- MIT Professional Education CPO certificate and MIT xPRO AI Strategy program (relevant to career goals)

Real results. Not hallucinations. That's the bar.

---

## Why This Matters: Tool Calling Is the Hard Part

Here's the thing most "run local LLMs" guides don't tell you: for a *chat* use case, almost any modern model works fine. Response quality scales roughly with parameter count, and even a 3b model can have a decent conversation.

But for an *agent* use case — where the model needs to interact with external systems, call APIs, read files, and chain multiple actions together — tool calling reliability is everything. A model that hallucinates tool results is worse than not running at all, because you don't know the output is wrong.

The reason smaller Ollama models struggle with this isn't (usually) intelligence — it's that they haven't been sufficiently fine-tuned on tool-use datasets in the instruction-following format your framework expects. The 27b Qwen3.5 model has had more capacity devoted to this during training, and it shows.

---

## The Routing Config I Ended Up With

Based on the test results, here's how I'm routing tasks now:

```
Heartbeats / periodic checks  →  phi3:mini          (free, fast, good enough)
Simple file writes             →  llama3.1:8b        (free, no tool calls needed)
Code generation                →  qwen2.5-coder:7b   (free, clean output)
Summaries / light research     →  qwen3.5:9b         (free, solid)
Overnight research + search    →  qwen3.5:27b-q4_K_M (free, tool calling works)
Complex reasoning / strategy   →  claude-sonnet-4-6  (paid, best quality)
Deep academic writing          →  claude-opus        (paid, ask first)
```

The goal is to push as much as possible to free local inference without sacrificing reliability. With this routing, the only tasks that hit my Anthropic API are the ones that genuinely need the quality gap — strategic thinking and complex writing. Everything else stays on-device.

Estimated monthly Anthropic API cost with this routing: **$3–8/month** versus the $15–20 I was burning when everything defaulted to Haiku or Sonnet.

---

## What Still Doesn't Work

Honest section, because these guides always gloss over the failure cases:

**Subagent tool calling with smaller models is still broken.** When I spin up an isolated subagent session with llama3.1:8b or phi3:mini, tool calls don't work reliably. The 27b model works fine in the *main* session where I tested it tonight, but I haven't fully validated it as a subagent yet. That's a test for another day.

**Speed is a real trade-off.** The 27b model is slow — noticeably so compared to the 8b and 9b options. For interactive use it's marginal, but for overnight batch tasks where latency doesn't matter, it's fine. Your tolerance will depend on your use case.

**The Vulkan GPU acceleration helps but isn't magic.** I'm getting meaningful speedups over CPU inference, but the 780M is still an integrated GPU — not an RTX 4090. If you need fast interactive inference with a 27b model, you need bigger iron.

---

## Next Steps

A few things I want to test:

- **qwen3.5:27b as a subagent** — does tool calling still work when it's running in an isolated session, or is this specific to main-session context? This matters a lot for autonomous overnight tasks.
- **Ollama's native web search API** — Ollama recently added a native web search integration. If that works reliably from subagent sessions, it could unlock a whole new tier of capability at zero cloud cost.
- **Multi-model pipelines** — using a fast cheap model for triage and routing, with the 27b only invoked when tool use or deep reasoning is actually needed.

I'll write those up as I go. If you're running a similar setup and want to compare notes, the [OpenClaw Discord](https://discord.com/invite/clawd) is a good place to find people doing the same thing.

{{< alert icon="circle-info" cardColor="#f1f5f9" iconColor="#94a3b8" textColor="#475569" >}}
**AI Assistance:** This post was researched and drafted with the assistance of Claude Sonnet 4.6 (Anthropic) via [OpenClaw](https://openclaw.ai). The research direction, test design, editorial decisions, and results are the author's own.
{{< /alert >}}
