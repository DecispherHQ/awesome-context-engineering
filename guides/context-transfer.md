---
layout: page
title: "How to Transfer Context Between AI Coding Sessions and Tools"
description: "Practical ways to carry working context across AI coding sessions and between tools like Claude Code and Cursor: rules files, handoff summaries, memory layers, and clipboard-style transfer."
permalink: /guides/context-transfer/
---

Every AI coding session ends the same way: the context window that slowly accumulated your task state, your constraints, and your dead ends gets thrown away. Tomorrow's session starts from zero. Switch tools mid-task (Cursor to Claude Code, or a laptop session to a CI agent) and the loss is immediate instead of overnight.

The cost isn't abstract. The agent re-explores files it already read, re-proposes approaches you already rejected, and re-makes mistakes you already corrected. You pay for those tokens twice and for your own time re-explaining.

Here's the toolbox, from zero-infrastructure upward.

## 1. Rules files: the durable baseline

Every major tool now reads a project-level instruction file: `CLAUDE.md` for Claude Code, `.cursor/rules` for Cursor, `AGENTS.md` as the emerging cross-tool convention. Whatever is in the file is in context every session, for every person on the team.

Rules files are the right home for *stable* context: build commands, conventions, architecture notes, known footguns. Their limit is equally clear: they're static. They can't carry "here's where I got to on the auth refactor at 6pm Tuesday," and they only update when a human edits them.

**Do this first.** A tight, current rules file eliminates the most repetitive re-explanation for free.

## 2. Handoff summaries: cheap and underrated

Before ending a session, ask the agent: *"Summarize this session for a successor agent: goal, current state, decisions made, rejected approaches, next steps."* Save it (`HANDOFF.md`, a scratch dir, wherever), and paste or reference it at the next session's start.

This is manual, and it works surprisingly well, because the model writing the summary knows what mattered. The failure mode is discipline: the day you forget is the day you needed it. Some teams automate the ritual with a session-end hook so the summary always gets written.

## 3. Memory layers: automatic recall

Memory tools observe sessions, extract facts, and retrieve them later: [Letta](https://github.com/letta-ai/letta) builds agents around editable memory blocks, [Mem0](https://github.com/mem0ai/mem0) and [Zep](https://github.com/getzep/zep) provide recall APIs and MCP servers that plug into coding tools.

Strengths: automatic, cross-session, queryable. The trade-off: general memory recalls *observations*, ranked by similarity. It's good at "what's the user's deploy setup," weaker at faithfully carrying a specific in-flight task state, and it has no native notion of which remembered statement was a binding decision.

## 4. Structured session transfer

The newest option treats mid-task state as a first-class object. [Decispher](https://decispher.com)'s Chat Clipboard is the clearest example: a `copy_chat` MCP tool serializes the current session (goal, state, decisions, discarded paths) and `paste_chat` rehydrates it in a different tool or a later session. Because both ends speak MCP, the same handoff works Cursor → Claude Code or human session → autonomous agent. Alongside it, Decispher's Branch Story resumes coding sessions with the project's decision history already loaded, so the "new session amnesia" gap closes from both directions.

## 5. What belongs where

The mistake to avoid is using one mechanism for everything. Context has layers with different lifetimes:

| Layer | Lifetime | Right home |
|---|---|---|
| Conventions, build setup | Months | Rules file (`CLAUDE.md` / `AGENTS.md`) |
| Team decisions and their "why" | Permanent | Decision system of record, served via MCP |
| Current task state | Hours-days | Handoff summary or structured transfer |
| Incidental facts | Variable | Memory layer, if at all |

Teams that conflate these end up with 400-line rules files full of stale task notes, or memory stores confidently recalling decisions that were later reversed. Short-lived state should travel in handoffs; permanent decisions should live somewhere that tracks supersession; and the rules file should stay short enough that the model actually weighs it.

Start with layers 1 and 2 today: they cost nothing. Add 3 or 4 when the team is large enough, or the agents autonomous enough, that manual discipline stops scaling.

---

*Part of [Awesome Context Engineering](../README.md). See also: [MCP servers for codebase context](mcp-servers-for-context.md) and [Reducing token costs in AI-assisted coding](reduce-token-costs.md).*
