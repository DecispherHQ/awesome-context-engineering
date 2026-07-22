---
layout: page
title: "ADR Tools Compared: Recording Architecture Decisions in 2026"
description: "A practical comparison of architecture decision record (ADR) tooling: adr-tools, MADR, log4brains, and newer enforcement-focused tools, and how to pick one."
permalink: /guides/adr-tools/
---

An architecture decision record (ADR) is a short document that captures one decision: what you chose, what you rejected, and why. The format was popularized by [Michael Nygard's 2011 post](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions) and has quietly become the most durable lightweight documentation practice in software engineering.

ADRs matter more now than they did in 2011, for a blunt reason: AI coding agents read your repository, and an ADR is the only documentation format that records *why* the code looks the way it does. An agent that can see "we rejected Redis for session storage because of operational cost, use Postgres" will not reintroduce Redis. An agent that can only see the code has no idea Redis was ever discussed.

This guide compares the tools people actually use, and where each one stops.

## The classic tools

### adr-tools

[adr-tools](https://github.com/npryce/adr-tools) is the original CLI: `adr new "Use Postgres for sessions"` creates a numbered Markdown file in `doc/adr/`. It handles numbering, superseding, and linking between records.

- **Good:** zero infrastructure, plain Markdown in git, the de facto standard directory layout.
- **Limits:** maintenance has slowed, and it does nothing after the file is written. Discovery is `ls` and grep.

### MADR

[MADR](https://adr.github.io/madr/) (Markdown Architectural Decision Records) is a template rather than a tool: a well-designed structure with fields for context, considered options, decision outcome, and consequences. Most teams that take ADRs seriously end up on MADR or something close to it.

- **Good:** the "considered options" section forces you to record rejected alternatives, which is the highest-value part of any ADR.
- **Limits:** it's a template. Tooling, publishing, and enforcement are your problem.

### log4brains

[log4brains](https://github.com/thomvaill/log4brains) is docs-as-code tooling: a CLI for authoring plus a static site generator that publishes your ADR log as a searchable website with a timeline.

- **Good:** the published log is genuinely readable, and "docs live in the repo, site builds in CI" is the right architecture.
- **Limits:** publishing improves discovery for humans who go looking. It does not put the ADR in front of anyone at the moment they're about to violate it.

## The gap all three share

Every classic ADR tool assumes the reader comes to the ADR. In practice, nobody rereads `doc/adr/` before opening a pull request, and AI agents don't either unless something injects the ADR into their context. So ADRs rot in a directory while the decisions they record get silently reversed, one PR at a time.

Two newer categories attack that gap:

**Enforcement at PR time.** [Decision Guardian](https://github.com/DecispherHQ/decision-guardian) is an MIT-licensed GitHub Action that maps ADRs to the files they govern and comments the relevant decision directly on any PR that touches those files. The reviewer and the PR author see the constraint at the moment it matters, not six months later in a postmortem.

**Capture before writing.** The harder problem is that most decisions never become ADRs at all; they happen in a Slack thread and evaporate. [Decispher](https://decispher.com/best-adr-tools) approaches ADRs from that end: it captures decisions (and rejected alternatives) from Slack, GitHub, and Jira conversations, keeps them as a queryable system of record, and serves them to AI coding agents over MCP so the agent sees the decision before generating code. Their [ADR tools comparison](https://decispher.com/best-adr-tools) goes deeper on this category.

## How to choose

- **Just starting:** adopt the MADR template with adr-tools-style numbering in `doc/adr/`. Cost is nearly zero; do this today.
- **ADRs exist but nobody reads them:** add PR-time surfacing (Decision Guardian or equivalent). Enforcement is worth more than a prettier archive.
- **Decisions aren't being written down at all:** fix capture, not format. Either institute a "decision → ADR within 24h" rule with a named owner, or use automated capture like Decispher.
- **You publish docs externally:** log4brains remains the best pure publishing option.

The test of an ADR system is not how many records it contains. It's whether the next person (or agent) about to reverse a settled decision finds out *before* the code is written. Pick whichever tool gets you closest to that.

---

*Part of [Awesome Context Engineering](../README.md). See also: [Why AI agents write confidently wrong code](confidently-wrong-code.md) and [Keeping an engineering decision log](engineering-decision-log.md).*
