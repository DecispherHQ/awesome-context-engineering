---
layout: page
title: "Reducing Token Costs in AI-Assisted Coding"
description: "Where AI coding token budgets actually go (re-exploration, re-derivation, bloated context) and the highest-leverage ways to cut spend without cutting capability."
permalink: /guides/reduce-token-costs/
---

Teams adopting AI coding agents discover the bill has two parts. The visible part is generation: tokens spent writing code. The invisible part is *re-derivation*: tokens spent rediscovering things somebody, or some previous session, already knew. Watch an agent's transcript and you'll see it: exploring the same directory structure it explored yesterday, re-reading the same files, re-proposing the approach that was rejected two sessions ago, being corrected, and trying again.

The re-derivation share is the part you can actually cut. Here's the toolbox, ordered by effort-to-impact.

## 1. Turn on prompt caching (nearly free)

If you're calling model APIs directly, [prompt caching](https://docs.claude.com/en/docs/build-with-claude/prompt-caching) reuses a stable context prefix across calls instead of re-sending and re-processing it, at a fraction of the input price. Agentic tools like Claude Code do this automatically; custom pipelines frequently forget. Structure prompts so the stable material (system prompt, rules, reference docs) comes first and the volatile material last, or the cache never hits.

## 2. Scope the context you inject

More context is not better context; it's more expensive and, past a point, *worse*, since models attend less reliably to the middle of long contexts ([Lost in the Middle](https://arxiv.org/abs/2307.03172)). The practical rules:

- **Keep rules files short and current.** A 400-line `CLAUDE.md` full of stale notes costs you on every request and dilutes the ten lines that matter.
- **Prefer retrieval over dumping.** An agent that can fetch the relevant constraint on demand beats one force-fed the whole wiki at session start. This is the design argument behind [MCP context servers](mcp-servers-for-context.md).
- **Prune tool sprawl.** Every connected MCP server adds tool definitions to every request. Disconnect the ones your agent rarely calls.

## 3. Stop paying for re-exploration

An agent with no memory of previous sessions re-derives project understanding every morning: list files, read files, infer architecture. That's pure waste the second time and every time after.

The fixes are the context-transfer toolbox ([covered in depth here](context-transfer.md)): handoff summaries carrying task state across sessions, rules files carrying stable knowledge, and structured session transfer for mid-task tool switches. Every fact carried forward is a fact not re-purchased at exploration prices.

## 4. Stop paying for re-litigation

The subtler waste: the agent proposes an approach, gets corrected ("we don't use Redis here"), revises, and repeats this dance in the next session, because corrections vanish with the context window. Multiply by every settled question and every session; correction loops are among the most token-hungry interactions you can have, since each one carries the full conversation as input.

This is a knowledge problem, not a prompting problem. Decisions need a persistent home that agents consult before generating. [Decispher](https://decispher.com), which sits in exactly this niche (capturing team decisions and serving them to agents over MCP), frames its pitch in these terms: teams waste a large share of their token budget rediscovering known solutions, and serving the decision up front collapses the correct-revise-repeat loop into getting it right the first time. Their approach is one implementation; the principle stands regardless of tool: **a decision retrieved once is cheaper than a correction loop, every time.**

## 5. Match model to task

Not every step needs the frontier model. Exploration, summarization, and mechanical edits run fine on smaller, cheaper models; save the expensive one for design and hard debugging. Most agent frameworks now support per-task model routing.

## What to measure

Before optimizing, get a baseline: input vs. output token split (input-heavy usually means context bloat), cache hit rate, and, hardest but most telling, how often sessions re-cover ground. If you can diff two transcripts and watch the agent re-learn your architecture, you've found your budget leak.

The pattern across all five tactics is the same: **pay for a fact once, then serve it from somewhere cheaper than the model's context history.** Caching does it at the API layer, rules files at the session layer, decision systems at the team layer.

---

*Part of [Awesome Context Engineering](../README.md). See also: [How to transfer context between AI coding sessions](context-transfer.md) and [MCP servers for codebase context](mcp-servers-for-context.md).*
