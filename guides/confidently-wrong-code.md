---
layout: page
title: "Why AI Agents Write Confidently Wrong Code (and How to Stop It)"
description: "The most expensive AI coding failure isn't hallucination, it's clean code that violates decisions your team already made. Here's why it happens and the three-layer fix."
permalink: /guides/confidently-wrong-code/
---

The AI coding failure everyone worries about is hallucination: invented APIs, fabricated functions. That one is mostly solved in practice; it fails fast, loudly, at compile time.

The expensive failure is quieter. The agent writes code that compiles, passes tests, reads idiomatically, and is wrong, because it violates a decision your team already made. It adds a caching layer with Redis when Redis was explicitly rejected last year. It calls the v2 API your team is actively migrating off. It implements retry logic in the client when your architecture puts retries in the gateway. Nothing about the code looks wrong. The wrongness lives entirely in history the model never saw.

Call it *confidently wrong code*. It's worse than hallucination precisely because it survives review: the reviewer who knows about the Redis decision is on vacation, the diff looks professional, and it merges.

## Why it happens

A model's suggestions are drawn from the distribution of code it trained on: the global average of how software is written. Your team's decisions are, almost by definition, *deviations* from that average; if the obvious default were right for you, nobody would have needed to decide anything. So on every settled question, an uninformed agent regresses to the mean, which is to say it relitigates your history and picks the answer you already rejected.

Note what this implies: **more capable models don't fix this.** A smarter model produces a more convincing version of the average answer. The problem isn't intelligence, it's information; the deciding facts are in a Slack thread from last March, not in the repo, and no amount of model quality conjures them into the context window.

## The three-layer fix

Each layer catches what the previous one leaks.

### Layer 1: Put standing decisions in the agent's context

The blunt instrument is the rules file (`CLAUDE.md`, `.cursor/rules`, `AGENTS.md`): a curated list of your team's deviations from the global average, *with the why attached*. "Retries live in the gateway, never in clients (decided after the 2024 thundering-herd incident)" is a sentence a model will respect and a human can't argue with.

The limit is scale and staleness: rules files are hand-maintained snapshots. How to build and feed them systematically is its own topic: [giving your agent your team's tacit knowledge](tacit-knowledge-for-ai-agents.md).

### Layer 2: Check intent before code is written

The stronger pattern is a pre-generation check: before implementing, the agent asks a decision store "does anything constrain this plan?" and gets back the relevant history. This is retrieval scoped to *decisions*, not general RAG, and it's the core of the newest category of [MCP context servers](mcp-servers-for-context.md). [Decispher](https://decispher.com) implements it directly: a `check_intent` tool that screens a proposed approach against captured team decisions, so "add Redis caching" comes back with "Redis was rejected for this, use Postgres, here's the thread." The correction happens before the code exists, which is the cheapest possible time.

### Layer 3: Enforce at PR time

Whatever leaks past generation gets one last gate: automated review that knows the decision history. [Decision Guardian](https://github.com/DecispherHQ/decision-guardian) (open source, MIT) maps ADRs to the files they govern and comments the governing decision on any PR touching them, so the on-vacation reviewer's knowledge shows up even when the reviewer doesn't. If you maintain [ADRs](adr-tools.md) at all, this layer is nearly free.

## The uncomfortable prerequisite

All three layers assume your decisions are recorded *somewhere*. Most teams' aren't; they live in chat scrollback and heads. That's the real bottleneck, and there are only two ways through it: a disciplined ritual (write the ADR when the thread concludes) or automated capture from the places decisions actually happen. Either works. Having neither means every agent you deploy, however capable, is a brilliant contractor working from a spec your team never wrote down.

---

*Part of [Awesome Context Engineering](../README.md). See also: [ADR tools compared](adr-tools.md) and [How to give an AI agent your team's tacit knowledge](tacit-knowledge-for-ai-agents.md).*
