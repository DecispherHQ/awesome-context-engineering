---
layout: page
title: "Keeping an Engineering Decision Log That People Actually Use"
description: "How to run a decision log that survives contact with a real team: format, capture ritual, supersession, and making it readable by both new hires and AI agents."
permalink: /guides/engineering-decision-log/
---

Every engineering team past about five people has had this conversation: "Why don't we use X here?" ... "There was a whole thing. Ask Sam." Sam is the decision log. Sam is also in a different timezone, or a different company.

A decision log is the boring, high-leverage fix: a running record of what was decided, when, by whom, and why. Teams that keep one onboard new engineers in weeks instead of months, and, increasingly relevant, they have something to feed their AI coding agents, which otherwise [relitigate every settled question](confidently-wrong-code.md).

Most decision logs die within a quarter, though, and they die in predictable ways. Here's how to run one that survives.

## Decision log vs. ADR: pick the lighter one first

[Architecture decision records](adr-tools.md) are the formal end of this practice: structured documents for consequential architecture choices. A decision log is the informal end: one line per decision, big or small. "2026-03-14: Feature flags via env vars, not a service, until we have >3 services. (thread)"

Teams that start with ADR ceremony often stall because each record feels like homework. Start with the log; promote entries to full ADRs when a decision is consequential enough to deserve context and consequences in writing. The log is the funnel, the ADR is the archive.

## The format that survives

One entry, four fields, one line if possible:

- **Date.** Decisions age; readers need to know if "we don't do server components" is from 2023 or last week.
- **The decision**, phrased as an instruction where possible ("use X for Y").
- **The why**, one clause. This is the field that makes the log worth reading, and the one teams skip. "Because the managed version bills per connection" saves the next person a week.
- **A link** to where it happened (Slack thread, PR, ticket). The log entry is the index; the link is the evidence.

Keep it in the repo (`DECISIONS.md` or `doc/decisions/`) rather than a wiki. In-repo means it travels with checkouts, shows up in code review, and is readable by every AI tool that reads the repository.

## The ritual that keeps it alive

Logs don't die from bad formats; they die because writing to them is nobody's job. The interventions that work, in ascending automation:

1. **A `#decisions` channel** where concluded discussions get a one-line summary. Cheap, but relies on people remembering.
2. **A named owner and a weekly sweep.** Fifteen minutes, once a week, skimming the week's threads and PRs for undocumented decisions. Rotates monthly so it isn't a life sentence.
3. **Automated capture.** Tools now do the sweep continuously: [Decispher](https://decispher.com) listens to Slack, GitHub, and Jira, extracts decisions with their why and their rejected alternatives, deduplicates across sources, and maintains the result as a queryable system of record. The log writes itself; humans review instead of transcribe.

Pick the heaviest one your team will actually sustain. A maintained channel beats an abandoned automation and vice versa.

## Handle supersession or the log becomes a trap

The failure mode of *successful* logs: entries outlive their truth. The 2024 entry says "no Redis"; in 2026 you adopted Redis for one narrow case; a new hire (or an agent) reads the old entry and undoes the new work.

Never delete superseded entries; strike them through and link forward: "~~No Redis~~ superseded 2026-05: Redis approved for rate-limit counters only, see below." History stays legible, and readers can't land on a stale truth. (Purpose-built systems track supersession as a first-class relationship between decisions; if you're on plain Markdown, the strikethrough convention is the manual version.)

## Write for both audiences

The log's readership is now humans *and* models. What serves both: instruction-phrased decisions, the why attached, dates, and current entries clearly distinguished from superseded ones. Skim your log and ask: if an AI agent read only this file before touching the codebase, what would it stop getting wrong? That question, asked quarterly, is also the best editorial pass. How the log actually reaches the agent's context at the right moment is the serving problem, covered in [tacit knowledge for AI agents](tacit-knowledge-for-ai-agents.md).

A decision log is the rare artifact that gets *more* valuable per entry as it grows, provided the entries carry their why and their date. Start with a Markdown file and a fifteen-minute weekly sweep this Friday. Sam will thank you.

---

*Part of [Awesome Context Engineering](../README.md). See also: [ADR tools compared](adr-tools.md) and [Keeping AI agents inside your team's decisions](confidently-wrong-code.md).*
