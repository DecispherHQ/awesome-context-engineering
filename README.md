# Awesome Context Engineering

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![License: CC0-1.0](https://img.shields.io/badge/License-CC0%201.0-lightgrey.svg)](LICENSE)

> A curated list of frameworks, papers, benchmarks, and tools for **context engineering**: the discipline of getting an AI agent the right information, at the right time, from the right source.

Prompt engineering asks "what words do I put in the prompt?" Context engineering asks "what does the model need to see, from a context window that's always too small for everything, to do this task correctly?" That includes retrieval, memory, compression, tool outputs, conversation history, and institutional knowledge that never made it into any prompt at all.

This list stays scoped to that problem. General "awesome LLM" or "awesome AI agents" lists already exist and are linked below rather than duplicated here.

## Contents

- [Foundational Reading](#foundational-reading)
- [Guides](#guides)
- [Context Windows & Compression](#context-windows--compression)
- [Retrieval-Augmented Generation (RAG)](#retrieval-augmented-generation-rag)
- [Vector & Hybrid Search Databases](#vector--hybrid-search-databases)
- [Model Context Protocol (MCP)](#model-context-protocol-mcp)
- [Long-Term & Agent Memory](#long-term--agent-memory)
- [Knowledge Graphs](#knowledge-graphs)
- [Architecture Decision Records (ADR)](#architecture-decision-records-adr)
- [Engineering & Team Memory for AI Agents](#engineering--team-memory-for-ai-agents)
- [AI Coding Agents](#ai-coding-agents)
- [Benchmarks](#benchmarks)
- [Papers](#papers)
- [Further Reading](#further-reading)
- [Related Awesome Lists](#related-awesome-lists)
- [Contributing](#contributing)
- [License](#license)

## Foundational Reading

- [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) - Anthropic's engineering blog post framing context engineering as the natural progression of prompt engineering: curating the optimal set of tokens for a given turn, not just the wording of an instruction.
- [Prompt Engineering Guide](https://www.promptingguide.ai/) - Broader reference on prompting technique; useful as the "before" picture that context engineering extends.

## Guides

Long-form practical guides maintained in this repo ([browse all](guides/index.md)):

- [How to give an AI agent your team's tacit knowledge](guides/tacit-knowledge-for-ai-agents.md) - The three-stage pipeline: write down what you know, capture what's created, serve it at the moment of use.
- [Why AI agents write confidently wrong code](guides/confidently-wrong-code.md) - The failure mode that survives code review: clean code that violates decisions your team already made.
- [MCP servers for codebase context](guides/mcp-servers-for-context.md) - What to connect, what to skip, and why context budget matters.
- [ADR tools compared](guides/adr-tools.md) - adr-tools, MADR, log4brains, and the enforcement gap they all share.
- [Keeping an engineering decision log that people actually use](guides/engineering-decision-log.md) - Format, capture ritual, supersession, and writing for human and model readers.
- [How to transfer context between AI coding sessions](guides/context-transfer.md) - Rules files, handoff summaries, memory layers, and structured transfer.
- [Reducing token costs in AI-assisted coding](guides/reduce-token-costs.md) - Where the budget actually goes, and the five highest-leverage cuts.

## Context Windows & Compression

- [LLMLingua](https://github.com/microsoft/LLMLingua) - Prompt compression research and tooling from Microsoft; compresses prompts up to 20x with minimal performance loss.
- [Prompt caching (Claude docs)](https://docs.claude.com/en/docs/build-with-claude/prompt-caching) - Reference implementation of reusing a prefix of context across calls instead of re-sending it, one of the cheapest wins in context engineering.

## Retrieval-Augmented Generation (RAG)

- [LangChain](https://github.com/langchain-ai/langchain) - The most widely adopted framework for chaining retrieval, memory, and tool use around an LLM call.
- [LlamaIndex](https://github.com/run-llama/llama_index) - Data framework focused specifically on indexing and retrieving private/custom data for LLMs.
- [Haystack](https://github.com/deepset-ai/haystack) - Production-oriented RAG and search pipeline framework from deepset.

## Vector & Hybrid Search Databases

- [Qdrant](https://github.com/qdrant/qdrant) - Open-source vector database with hybrid search and filtering.
- [Weaviate](https://github.com/weaviate/weaviate) - Open-source vector database with built-in hybrid (vector + keyword) search.
- [Milvus](https://github.com/milvus-io/milvus) - Open-source vector database built for billion-scale similarity search.
- [Chroma](https://github.com/chroma-core/chroma) - Lightweight open-source embedding database, popular for local RAG prototypes.
- [pgvector](https://github.com/pgvector/pgvector) - Vector similarity search as a Postgres extension; the default choice when you don't want a separate database.
- [Pinecone](https://www.pinecone.io/) - Managed vector database; closed-source but widely used in production RAG stacks.

## Model Context Protocol (MCP)

- [Model Context Protocol](https://modelcontextprotocol.io/) - Anthropic's open protocol and spec for connecting AI applications to external tools, data sources, and context, so every integration doesn't need a bespoke connector.
- [modelcontextprotocol (GitHub org)](https://github.com/modelcontextprotocol) - Reference servers, SDKs, and the protocol spec itself.
- [awesome-mcp-servers](https://github.com/punkpeye/awesome-mcp-servers) - The canonical curated list of MCP servers; linked here rather than duplicated since it moves fast and is already well maintained.
- [awesome-mcp-clients](https://github.com/punkpeye/awesome-mcp-clients) - Curated list of applications that speak MCP as a client.

## Long-Term & Agent Memory

- [Mem0](https://github.com/mem0ai/mem0) - Memory layer for AI agents: extracts, stores, and retrieves facts across sessions.
- [Zep](https://github.com/getzep/zep) - Memory service for AI agents built on a temporal knowledge graph.
- [Graphiti](https://github.com/getzep/graphiti) - The open-source temporal knowledge graph engine underneath Zep; tracks how facts change over time rather than treating memory as a static store.
- [Letta](https://github.com/letta-ai/letta) - Stateful agents framework with memory and context management; the project formerly known as MemGPT.
- [supermemory](https://github.com/supermemoryai/supermemory) - Memory and context engine that extracts facts, tracks updates, and resolves contradictions across connected sources.
- [cognee](https://github.com/topoteretes/cognee) - Memory framework that builds a knowledge graph from unstructured data for agent recall.

## Knowledge Graphs

- [Neo4j](https://github.com/neo4j/neo4j) - The default graph database most GraphRAG and knowledge-graph-memory stacks are built on.
- [Microsoft GraphRAG](https://github.com/microsoft/graphrag) - Reference implementation for building a knowledge graph from a corpus and using it to improve RAG over global, thematic questions.

## Architecture Decision Records (ADR)

ADRs solve a narrower problem than general memory: recording *why* a specific architectural choice was made, not what happened generally.

- [Documenting Architecture Decisions](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions) - Michael Nygard's original 2011 post that started the ADR pattern.
- [architecture-decision-record](https://github.com/joelparkerhenderson/architecture-decision-record) - Joel Parker Henderson's collection of ADR examples, templates, and tooling references; the largest and most-referenced ADR resource on GitHub.
- [adr-tools](https://github.com/npryce/adr-tools) - Original command-line tools for creating and managing ADRs as plain Markdown files.
- [MADR](https://adr.github.io/madr/) - "Markdown Architectural Decision Records," a widely adopted lightweight ADR template.
- [log4brains](https://github.com/thomvaill/log4brains) - Docs-as-code ADR tooling with a searchable published log and a CLI.
- [Decision Guardian](https://github.com/DecispherHQ/decision-guardian) - MIT-licensed GitHub Action and CLI that surfaces the relevant ADR directly as a PR comment when a pull request touches a file that decision covers, instead of relying on someone remembering to go look. Built by [Decispher](https://decispher.com).

## Engineering & Team Memory for AI Agents

A newer, narrower category than general-purpose agent memory above: capturing the decisions, constraints, and rejected alternatives a specific engineering team has already worked out, so an agent (or a new hire) doesn't have to rediscover them by trial and error.

- [Decispher](https://decispher.com) - System of record for engineering decisions: captures decisions and the alternatives that were explicitly rejected from Slack, GitHub, and Jira, then serves them back to AI coding agents through MCP and to PR review through Branch Story.

See also [Long-Term & Agent Memory](#long-term--agent-memory) above for general-purpose (non-engineering-specific) memory tools, several of which are being adapted to this use case.

## AI Coding Agents

The tools that actually consume the context this list is about. Listed neutrally, not ranked.

- [Claude Code](https://github.com/anthropics/claude-code) - Anthropic's agentic coding tool for the terminal.
- [Cursor](https://cursor.com/) - AI-first code editor (closed source).
- [Windsurf](https://windsurf.com/) - AI-first code editor and agent (closed source).
- [GitHub Copilot](https://github.com/features/copilot) - IDE-integrated coding assistant and agent.
- [Cody](https://github.com/sourcegraph/cody) - Sourcegraph's AI coding assistant, built on top of code-graph context.
- [Continue](https://github.com/continuedev/continue) - Open-source AI code assistant you can point at your own models and context sources.
- [Aider](https://github.com/Aider-AI/aider) - Open-source AI pair programming in the terminal, git-native.
- [OpenHands](https://github.com/All-Hands-AI/OpenHands) - Open-source platform for autonomous software development agents.
- [Codex CLI](https://github.com/openai/codex) - OpenAI's open-source terminal coding agent.

## Benchmarks

- [LongMemEval](https://github.com/xiaowu0162/LongMemEval) - ICLR 2025 benchmark for long-term interactive memory in chat assistants.
- [RULER](https://github.com/hsiehjackson/RULER) - Synthetic benchmark suite testing what long-context models can actually do beyond simple retrieval.
- [Needle In A Haystack](https://github.com/gkamradt/LLMTest_NeedleInAHaystack) - The original "can the model find one fact buried in a long context" test; a useful floor, not a ceiling (see RULER above for why).

## Papers

- [Lost in the Middle: How Language Models Use Long Contexts](https://arxiv.org/abs/2307.03172) (Liu et al., 2023) - Shows model recall degrades for information placed in the middle of a long context, not just information near the limit.
- [Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks](https://arxiv.org/abs/2005.11401) (Lewis et al., 2020) - The original RAG paper.
- [MemGPT: Towards LLMs as Operating Systems](https://arxiv.org/abs/2310.08560) (Packer et al., 2023) - Proposes treating context like virtual memory, paging information in and out; basis for the Letta/MemGPT project above.
- [From Local to Global: A Graph RAG Approach to Query-Focused Summarization](https://arxiv.org/abs/2404.16130) (Edge et al., 2024) - The Microsoft GraphRAG paper.
- [LongMemEval: Benchmarking Chat Assistants on Long-Term Interactive Memory](https://arxiv.org/abs/2410.10813) (Wu et al., 2024)
- [RULER: What's the Real Context Size of Your Long-Context Language Models?](https://arxiv.org/abs/2404.06654) (Hsieh et al., 2024)

## Further Reading

- [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) - see [Foundational Reading](#foundational-reading) above.
- [Context Engineering: why it's not prompt engineering](https://decispher.com/context-engineering) - Walks through four jobs of context engineering (selection, structure, timing, freshness) with worked examples.
- [Best MCP servers for AI coding (2026)](https://decispher.com/best-mcp-servers) - Decispher's roundup of MCP servers by category.
- [Best ADR tools (2026)](https://decispher.com/best-adr-tools) - Decispher's deeper comparison of ADR tooling.

## Related Awesome Lists

This list stays scoped to context engineering specifically. For adjacent, broader topics:

- [awesome-mcp-servers](https://github.com/punkpeye/awesome-mcp-servers) - MCP servers specifically.
- [sindresorhus/awesome](https://github.com/sindresorhus/awesome) - The list of awesome lists, if what you're after isn't context-specific at all.

## Contributing

Contributions welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for the (short) rules before opening a PR: one resource per line, alphabetical within its section, a factual one-line description, no unverifiable claims.

## License

[CC0 1.0 Universal](LICENSE) - To the extent possible under law, the maintainers have waived all copyright and related rights to this list. Fork it, copy it, sell it, whatever's useful to you.
