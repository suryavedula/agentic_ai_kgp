---
description: "Architecture deep dive - run one lens (trace, deletion, data, failure, agent, adr, trust) and save it to _architecture_brief/deep-dives/"
agent: agent
model: Claude Opus 5.5
---

Follow the **Ground rules** and **Style** sections of [the repo-architecture-brief skill](../skills/repo-architecture-brief/SKILL.md):
evidence with file paths, [Confirmed] / [Inferred] / [Unknown] tags, no secrets, read-only on repos.

Start from `_architecture_brief/ARCHITECTURE_BRIEF.md` and `_architecture_brief/repos/*.md` to orient yourself,
then open source code to verify and go deeper. If those files don't exist yet, say so and stop.

**Lens to run:** ${input:lens:trace | deletion | data | failure | agent | adr | trust | all}
**Focus (optional):** ${input:focus:a flow, repo, or component to focus on - leave empty to choose the most important one}

Write the result to `_architecture_brief/deep-dives/<lens>.md` (overwrite if it exists).
If the lens is `all`, run the lenses one by one in the order below, writing each file before starting the next.

---

## Lens: trace - follow one request end to end
Pick the most important user-facing flow (or the focus above). Trace it across all repos:
entry point -> each service/function -> queues/DBs -> LLM calls -> tools -> response.
For every hop: file:line, data shape passed, sync vs async, timeout/retry behaviour.
Draw a Mermaid `sequenceDiagram`. End with the 3 places this flow is most likely to fail and why.

## Lens: deletion - why does each part exist (first principles)
For each major component:
1. What problem forced it to exist (the constraint, not the feature)?
2. If deleted tomorrow, what breaks first, and for whom?
3. What is the simplest possible replacement, and why wasn't it chosen?
4. A one-line real-world analogy a 10-year-old would understand.

## Lens: data - who owns the truth
List every important piece of state (entities, embeddings, prompts, agent memory, config, secrets).
Table: state | source of truth | writers | readers | copies/caches | consistency (strong/eventual) | refresh | what happens when stale.
Flag data with two writers or no clear owner.

## Lens: failure - break it on purpose
For each failure, describe current behaviour (cite code), what the user sees, and what it SHOULD do:
- LLM provider slow (10s) or down
- vector store returns empty or stale results
- a tool/API the agent calls returns garbage or times out
- agent loops or exceeds its token/cost budget
- a deploy ships a bad prompt or bad config
- a downstream system (ticketing system / DB / queue) is down
Summary table: failure | current behaviour | blast radius | missing safeguard.

## Lens: agent - anatomy of the agent loop
For every agent / LLM-driven flow:
trigger -> context assembly (what enters the prompt, from where, how it is ranked/trimmed)
-> model call (model, params, structured output?) -> tool selection and execution (tools, permissions, side effects, idempotency)
-> memory (short/long term) -> guardrails at each step -> human-in-the-loop points -> evaluation and logging.
Then answer: where does non-determinism enter, what is the worst action a tool could take, and what stops it?
Include a Mermaid `flowchart` of the loop.

## Lens: adr - rebuild the decisions
Find the 6-8 most significant architecture decisions visible in code/config
(framework, vector DB, sync vs async, single vs multi-agent, hosting, IaC, etc.).
Each as a mini ADR: Context | Options likely considered | Decision | Consequences (good and bad) | Would you still choose it today?

## Lens: trust - trust boundaries (regulated environment)
Mermaid `flowchart` of trust boundaries: user, internal services, LLM provider, vector store, external APIs, logs.
For every boundary crossing: data that crosses (PII? confidential?), authn/authz, encryption, logging/audit trail, data residency.
End with the top 5 things an auditor or security architect would challenge.

---

## Every lens ends with
- **Interview angle**: 3 questions an interviewer could ask from this lens, each with a short model answer from [Confirmed] facts.
- **Gaps**: anything [Inferred] or [Unknown] that I should verify or recall myself.

When done: list the file(s) written and the single most surprising finding.
