---
title: "Getting Ollama Working Properly with OpenClaw: Three Config Bugs and How to Fix Them"
subtitle: "Subagents silently falling back to paid cloud models. Tool calls returning garbage. Wrong config keys. Here's everything that went wrong — and the exact fixes."
date: 2026-03-19
series: ["homelab"]
series_order: 3
tags: ["homelab", "AI", "ollama", "openclaw", "self-hosted", "local-AI"]
categories: ["The Homelab"]
draft: true
description: "Getting Ollama models working reliably with OpenClaw took longer than expected. Three separate bugs — subagent auth failure, tool call failures with small models, and wrong config structure. Here are the fixes with copy-paste config blocks."
---

> **TL;DR:** Three bugs bit me getting Ollama working with OpenClaw. (1) Subagents silently fell back to paid Claude without warning — fix is adding `"apiKey": "ollama-local"` as a placeholder. (2) Small models under ~14B can't reliably call tools — use qwen3.5:27b for anything requiring `web_search` or `exec`. (3) Brave Search config belongs at `tools.web.search`, not at the root level. All five models now tested and working.

---

## What I Was Trying to Do

My homelab runs two machines relevant to this:

- **MeLE Quieter 4C** (Intel N100, 16GB RAM) — runs OpenClaw as an LXC container on Proxmox
- **MINISFORUM UM790 Pro** (Ryzen 9 7940HS, 64GB RAM, Radeon 780M) — runs Ollama, serving models at `192.168.4.52:11434`

The goal was straightforward: route as many tasks as possible to free local models instead of paid Anthropic API calls. OpenClaw supports Ollama via its OpenAI-compatible completions API, so in theory this should be a config-and-go situation.

It was not.

---

## Problem 1 — Subagents Silently Falling Back to Cloud

This one took a while to spot because it fails invisibly.

When OpenClaw spawns a subagent, it creates a new isolated session and passes through the provider config. The issue: OpenClaw's auth pipeline treats an Ollama provider with *no* `apiKey` as an unauthenticated local endpoint and doesn't forward the credentials to the subagent session. The subagent starts up, can't find valid Ollama credentials, and silently falls back to whatever paid model is configured as default.

You don't get an error. The subagent just... runs on Claude Haiku and bills your Anthropic account. You only see it if you're watching gateway logs:

```bash
tail -f /tmp/openclaw/openclaw-*.log | grep -i "model\|provider\|fallback"
```

This is a known bug — tracked in [openclaw/openclaw#43945](https://github.com/openclaw/openclaw/issues/43945) ("Subagents miss Ollama credentials and silently fall back to cloud models"). The workaround until it's patched: add a dummy `apiKey` value to your Ollama provider block.

**Fix:**

```json
"ollama": {
  "baseUrl": "http://192.168.4.52:11434/v1",
  "api": "openai-completions",
  "apiKey": "ollama-local",
  "models": [...]
}
```

The string `"ollama-local"` is a placeholder — Ollama doesn't actually validate API keys. But it satisfies OpenClaw's auth pipeline and gets passed to subagent sessions correctly.

---

## Problem 2 — Tool Calls Failing or Producing Garbage

Once subagents were actually hitting Ollama, the next problem: small models couldn't reliably call tools.

`phi3:mini` and `llama3.1:8b` would either ignore available tools entirely or produce malformed tool call JSON that the framework couldn't parse. For tasks like `web_search` or `exec`, this meant silent failures — the model would either skip the tool call or hallucinate that it had run it and return fabricated results.

This is well-documented in [openclaw/openclaw#41871](https://github.com/openclaw/openclaw/issues/41871) ("Local Ollama models still hang in OpenClaw 2026.3.8"), and the community consensus (from [haimaker.ai/blog/best-local-models-for-openclaw](https://haimaker.ai/blog/best-local-models-for-openclaw)) is blunt: **models under ~14B parameters are unreliable for tool calling in agentic frameworks**. They either haven't been sufficiently trained on tool-use datasets, or they don't have the capacity to correctly format structured JSON while also reasoning about what to do.

The fix here isn't a config change — it's model selection. More on that below.

---

## Problem 3 — Wrong Config Path for Brave Search

My initial attempt at adding Brave Search put it at the root config level:

```json
{
  "search": {
    "provider": "brave",
    "apiKey": "..."
  }
}
```

OpenClaw rejected this silently (no error, just no search). The correct path, per the [official docs](https://docs.openclaw.ai/brave-search), is nested under `tools.web.search`:

```json
{
  "tools": {
    "web": {
      "search": {
        "provider": "brave",
        "apiKey": "YOUR_BRAVE_API_KEY",
        "maxResults": 5,
        "timeoutSeconds": 30
      }
    },
    "profile": "full"
  }
}
```

Worth noting: `"profile": "full"` is what exposes the full tool set to models. Without it, some tools (including `web_search`) may not appear in the model's context.

---

## Fix 3 — Model Config Fields That Actually Matter

Beyond model selection, each model entry needs a few specific fields or you'll hit timeouts and context overflow:

```json
{
  "id": "qwen3.5:27b-q4_K_M",
  "name": "Qwen3.5 27B (Q4)",
  "reasoning": false,
  "contextWindow": 32768,
  "maxTokens": 4096
}
```

**`"reasoning": false`** — critical. When this is missing or true, OpenClaw sends the system prompt using the `developer` role. Ollama doesn't support the developer role and either errors or silently drops the system prompt. Result: the model has no instructions and behaves erratically.

**`contextWindow`** — must match the model's actual limit. If it's set too high, you'll get truncation errors or hangs:
- phi3:mini: `4096`
- llama3.1:8b: `32768`
- qwen2.5-coder:7b: `32768`
- qwen3.5:9b: `32768`
- qwen3.5:27b-q4_K_M: `32768`
- nomic-embed-text: `8192`

**`maxTokens`** — limits response length. Without this, some models will generate until they hit the context window, causing hangs.

---

## Model Selection: Which Model Does What

With the config sorted, I tested all five models. The routing I landed on:

| Model | Params | Task |
|---|---|---|
| phi3:mini (Microsoft) | 3.8B | Heartbeats, simple tasks, no tool calling |
| llama3.1:8b (Meta) | 8B | File ops and workspace tasks |
| qwen2.5-coder:7b (Alibaba) | 7B | Code generation |
| qwen3.5:9b (Alibaba) | 9B | Research summaries, file reading |
| qwen3.5:27b-q4_K_M (Alibaba) | 27B | Overnight research + web search |

The small models are fine for tasks that don't require tools. The moment you need `web_search` or complex chained tool calls, you need the 27b.

---

## Test Results

Live tests, all five models in sequence:

- **phi3:mini** → responded cleanly, fast ✅
- **qwen2.5-coder:7b** → wrote clean Python function ✅
- **qwen3.5:9b** → read and accurately summarised a long profile file ✅
- **llama3.1:8b** → listed workspace files via `exec` ✅
- **qwen3.5:27b** → read the profile file, identified top 3 priorities, ran **three separate `web_search` calls** with real results ✅

That last one is the important result. Three real web searches — not hallucinated, not skipped. The model correctly formed the tool call JSON, waited for real results, and incorporated them into its response. For overnight autonomous research tasks, this means I can route them entirely to free local inference.

---

## Cost Impact

Before this work, most tasks — including overnight research subagents — were hitting Claude Haiku or Sonnet by default.

| | Before | After |
|---|---|---|
| Anthropic API spend | ~$15–20/month | ~$3–8/month |
| Local inference cost | $0 | $0 |
| Saving | — | ~70% |

The UM790 Pro cost $1,479 AUD. At ~$12/month saved, it pays for itself on API savings in about 10 years — which is obviously not the financial justification. The real reasons are privacy (conversations stay on-device), speed (no round-trip latency for simple tasks), and the ability to run models 24/7 for batch tasks without watching a spend counter tick up.

**One honest caveat:** GPU acceleration on the UM790 Pro is not working yet. All inference is currently on CPU. The Radeon 780M is there, it's just blocked by ROCm incompatibility with Proxmox's patched kernel. A follow-up post will cover that fix — [the Vulkan workaround I documented earlier](/posts/homelab-01-ollama-vulkan-amd-proxmox) gets GPU acceleration working on the bare metal, but I haven't validated it inside a Proxmox LXC yet. CPU inference with the 7940HS is still fast enough to be practical, just not as fast as it will be.

---

## What's Next

- Validate qwen3.5:27b as a subagent (tonight's tests were in the main session — subagent tool calling still unconfirmed)
- Test Ollama's native web search API as an alternative to Brave for subagent sessions
- GPU acceleration inside Proxmox LXC — the Vulkan fix needs validating in a container context

---

## References

- [openclaw/openclaw#43945](https://github.com/openclaw/openclaw/issues/43945) — Subagents miss Ollama credentials and silently fall back to cloud models
- [openclaw/openclaw#41871](https://github.com/openclaw/openclaw/issues/41871) — Local Ollama models still hang in OpenClaw 2026.3.8
- [haimaker.ai — Best Local Models for OpenClaw](https://haimaker.ai/blog/best-local-models-for-openclaw) — community consensus on model selection and minimum parameter thresholds
- [OpenClaw Brave Search docs](https://docs.openclaw.ai/brave-search) — correct config path for web search

{{< alert icon="circle-info" cardColor="#f1f5f9" iconColor="#94a3b8" textColor="#475569" >}}
**AI Assistance:** This post was researched and drafted with the assistance of Claude Sonnet 4.6 (Anthropic) via [OpenClaw](https://openclaw.ai). The problems, fixes, and test results are the author's own experience.
{{< /alert >}}
