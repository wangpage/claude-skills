# Pattern Library

Accumulated design patterns the user has found worth remembering across AI
systems, agent frameworks, and infra. This is **load-on-demand** — most runs
don't need it. Pull this in only when an OSS/paper item looks like it
matches one of the patterns below.

## What this file is *not*

- A tutorial on software architecture.
- A catalogue of every project on GitHub.
- A place to stash trending buzzwords.

## What this file *is*

A shortlist of patterns that keep re-appearing in the AI/agent infra space.
For each pattern, the row has: **name**, **one-line definition**, **when to
apply**, **a canonical example** (framework or paper), **tagging hint**.

When the AI Radar extractor sees an OSS/paper item that matches a pattern,
it appends `#patterns: <name>` inline on the item row. The user then
accumulates a mental map over time: "oh, this is just the X pattern
restated".

## The seed list

| Name | Definition | When to apply | Canonical example | Tagging hint |
|---|---|---|---|---|
| **plugin system** | Core runtime defines stable extension points; third parties ship capabilities as loadable units. | When the product must extend without a redeploy; when the vendor cannot enumerate use cases. | VS Code extension API; Claude Code skills. | `#patterns: plugin-system` |
| **event bus** | Components publish events to a central channel; consumers subscribe to types, not senders. | Multi-agent workflows; decoupling producer/consumer cadence. | ROS topics; agent frameworks with `on_event`. | `#patterns: event-bus` |
| **layered architecture** | Strict top-to-bottom dependency: presentation → domain → data. No upward calls. | Long-lived systems; teams that span specialties. | Classic web apps; enterprise AI platforms. | `#patterns: layered` |
| **microkernel** | Minimal core; everything else is a plugin including first-party features. | When you expect the "core" to outlive every feature. | Emacs; some agent frameworks. | `#patterns: microkernel` |
| **actor model** | Independent units communicate only via asynchronous messages; no shared mutable state. | Concurrency under backpressure; agents that must not block each other. | Erlang/OTP; Akka; newer multi-agent systems. | `#patterns: actor-model` |
| **memory abstraction** | Unified interface over working memory, session memory, long-term store; caller does not know the backing store. | Agent systems that outlive a single conversation; cross-session continuity. | MemOS-style frameworks; Claude Code's memory system. | `#patterns: memory-abstraction` |
| **tool-use contract** | Agents call tools via a typed schema; tool authors do not know about agents; agents do not know about tool internals. | Any agent framework with >1 tool. | Anthropic / OpenAI tool-use APIs; MCP. | `#patterns: tool-use` |
| **retrieval + generation (RAG)** | Query an external store, condition generation on retrieved chunks. | When the model needs facts it wasn't trained on, and hallucination is costly. | Most enterprise search assistants. | `#patterns: rag` |
| **eval harness** | Automated scoring pipeline that runs fixed prompts against a model and produces a scalar you trust. | Before shipping any model-in-the-loop product. | `lm-eval-harness`; custom CI-run evals. | `#patterns: eval-harness` |
| **prompt caching** | Stable prefix is reused across requests; provider serves cached tokens cheaper / faster. | Agent loops; long system prompts; large reference documents. | Anthropic prompt caching; OpenAI prompt caching. | `#patterns: prompt-caching` |

## How to add a pattern

- Only add a pattern you've seen **≥3 times** in distinct projects. One
  occurrence is not a pattern, it's a coincidence.
- Keep the definition to one sentence. If you need a paragraph, the pattern
  is not crisp enough yet.
- Always give a canonical example — without one, the pattern is abstract and
  the user will forget why it matters.
- Prefer **specific** names over fashionable ones. "Memory abstraction" is
  better than "cognitive architecture".

## How to remove a pattern

If a pattern hasn't been tagged in 10 consecutive runs, it is probably not
useful here and should be pruned. Retain only what you actually use.

## What this file is *for*, ultimately

Projects and frameworks churn; patterns do not. The goal of AI Radar is not
to list every new repo — it's to turn "find more projects" into "recognize
more patterns". The pattern tag on each OSS/paper row is how this file
actually earns its place in the workflow.
