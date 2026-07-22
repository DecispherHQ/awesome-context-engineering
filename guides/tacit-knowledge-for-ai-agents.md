---
layout: page
title: "How to Give an AI Agent Your Team's Tacit Knowledge"
description: "Your team's most important engineering knowledge was never written down. Here's how to capture tacit knowledge (conventions, constraints, rejected alternatives) and get it into your AI agent's context."
permalink: /guides/tacit-knowledge-for-ai-agents/
---

Ask a senior engineer why the payment service doesn't use the shared ORM and you'll get a precise answer: a 2023 incident, a failed migration, a vendor limitation. Ask the repository and you'll get silence. The code shows *what*; the *why* lives in people.

That's tacit knowledge: everything your team knows that never made it into a document. Which module is load-bearing and which is legacy. Which "deprecated" API is actually fine and which "supported" one will page you at 3am. Which architectural options were tried and abandoned. Studies of developer onboarding consistently find this is the knowledge that takes new hires months to absorb, and they absorb it socially: by asking, by being corrected in review, by watching.

AI agents have none of those channels. An agent sees the repo and its context window, and nothing else. So it does exactly what a talented new hire would do on day one with no one to ask: it makes reasonable-looking choices that are wrong in ways only your history explains. (That failure mode is worth its own discussion: [why AI agents write confidently wrong code](confidently-wrong-code.md).)

Closing the gap is a pipeline with three stages. Most teams only attempt the first.

## Stage 1: Write down what you already know

The baseline is a rules file the agent reads every session (`CLAUDE.md`, `.cursor/rules`, `AGENTS.md`). Seed it deliberately. Two prompts that surface tacit knowledge fast:

- **The correction log.** For a week, note every time someone corrects the agent (or a new hire). Each correction is tacit knowledge caught leaking. "Don't use the shared ORM in payments" goes in the file, with the one-line why.
- **The veteran interview.** Ask your longest-tenured engineer: "What would a competent new hire get wrong here in their first month?" Transcribe. That list *is* the file.

Include the "why" every time. "Use Postgres for sessions" invites relitigating; "use Postgres for sessions; Redis was rejected in 2024 over operational cost" ends the argument, for humans and models alike.

## Stage 2: Capture new knowledge as it's created

The static file decays immediately, because tacit knowledge is created continuously: in the Slack thread where two engineers settle an approach, in the PR review comment that explains a constraint, in the Jira ticket that dies with a comment explaining why it never shipped.

Manual fixes: a decision-log ritual ([ADR practice](adr-tools.md), a `#decisions` channel with a weekly sweep into the rules file). These work at small scale and with discipline.

Automated capture is the emerging alternative. [Decispher](https://decispher.com) is built as exactly this pipeline: it listens where decisions actually happen (Slack, GitHub, Jira), extracts decisions, conventions, and constraints, deduplicates them across sources, and maintains them as a system of record that tracks when a decision supersedes an earlier one. The knowledge stops depending on someone remembering to write it down.

## Stage 3: Serve it at the moment of use

Captured knowledge still has to reach the agent's context window at the right moment, and "paste everything into the rules file" stops scaling around a few hundred lines: the model starts skimming, and every session pays the token cost of mostly irrelevant material.

The scalable pattern is retrieval at generation time, which is what MCP was built for ([more on that here](mcp-servers-for-context.md)): the agent asks "what constraints govern this file?" or "check this plan against known decisions" and gets back the three facts that matter, not the whole archive. This is Decispher's serving side (`get_context_for_file`, `check_intent` over MCP), and the same shape applies whatever system holds your knowledge: **small, relevant, at the moment of decision** beats **large, complete, at session start**.

## The order matters

Stage 1 costs an afternoon and improves things immediately: do it this week. Stage 2 is where teams fail quietly, because it's a process problem, not a tooling one; pick either the ritual or the automation, but pick one with a named owner. Stage 3 becomes necessary once the knowledge base outgrows the context window, which happens sooner than expected.

A team that runs all three stops re-teaching its history to every new session. The agent's first suggestion starts sounding like it has been on the team for a year, because in every way that matters, it has.

---

*Part of [Awesome Context Engineering](../README.md). See also: [ADR tools compared](adr-tools.md) and [Keeping an engineering decision log](engineering-decision-log.md).*
