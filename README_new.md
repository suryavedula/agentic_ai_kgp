---
name: repo-architecture-brief
description: Analyze every repository under a projects folder and produce an architecture brief - HLD, use cases, challenges and fixes, agents, RAG, embeddings, guardrails, deployment pipeline, and SDLC / Agentic Development Life Cycle mapping - plus an interview cheatsheet and scenario-based drill-down questions. Use when asked to summarize, document, explain, or prepare for interviews about the architecture of multiple repos.
---

# Multi-Repo Architecture Brief

You are a principal architect writing an evidence-based brief about a portfolio of repositories.
Work in two phases: **Map** (one repo at a time, small notes) then **Reduce** (combine notes into one brief).
Never load every repo into context at once.

## Ground rules

- **Evidence or it didn't happen.** Every claim cites a file path, config key, commit, or PR. Format: `(repo/path/file.ext)`.
- Tag each finding: **[Confirmed]** seen in code/config, **[Inferred]** deduced from naming/structure, **[Unknown]** not found.
- If something (e.g. RAG, guardrails) does not exist, say "Not found" - do not invent it.
- Never print secrets, tokens, connection strings, customer data, or internal hostnames. Refer to them as `<redacted>`.
- Read-only: do not modify any repo. Only write into `./_architecture_brief/`.

## Phase 0 - Inventory

1. List every top-level folder that contains `.git`. Write `_architecture_brief/00-inventory.md` with a table:
   repo | primary language(s) | type (service / library / infra / pipeline / frontend / agent / data) | last commit date | size (files).
2. Skip vendored, archived, or generated folders (`node_modules`, `vendor`, `dist`, `.terraform`, `target`, `build`).

## Phase 1 - Map (one repo at a time)

For each repo, read in this order and stop once the picture is clear:
`README*`, `docs/`, `adr/` or `decisions/`, `AGENTS.md`, `.github/copilot-instructions.md`,
build files (`package.json`, `pom.xml`, `build.gradle`, `pyproject.toml`, `go.mod`, `*.csproj`),
`Dockerfile`, `docker-compose*`, `helm/`, `charts/`, `k8s/`, `*.tf`, `bicep`,
CI files (`.github/workflows/`, `azure-pipelines*.yml`, `.gitlab-ci.yml`, `Jenkinsfile`),
entry points (`main`, `app`, `server`, `cmd/`), config (`application*.yml`, `.env.example`, `values*.yaml`).

Then search the repo for these signals (use text search, not full reads):

| Concern | Search for |
|---|---|
| LLM / AI calls | openai, anthropic, azure-openai, bedrock, vertex, langchain, langgraph, llamaindex, semantic-kernel, autogen, crewai, chat.completions, messages.create |
| Agents | agent, tool_call, function_call, tools=, planner, orchestrator, mcp, a2a, handoff, subagent |
| RAG | retriever, vectorstore, chunk, rerank, similarity_search, top_k, context window |
| Embeddings / vector DB | embedding, embed(, pgvector, qdrant, pinecone, weaviate, chroma, faiss, milvus, opensearch knn, ai search |
| Guardrails | guardrail, moderation, content_filter, pii, redact, presidio, nemo, llm-guard, prompt injection, allowlist, schema validation, max_tokens, rate limit |
| Evals / quality | eval, ragas, deepeval, promptfoo, golden set, judge, benchmark |
| Observability | opentelemetry, langfuse, langsmith, prometheus, grafana, app insights, tracing |
| Security / compliance | vault, key vault, secret, rbac, opa, kyverno, sast, trivy, snyk, codeql, sbom, audit log |
| Workflow / BPM | flowable, camunda, temporal, airflow, step functions |

Write `_architecture_brief/repos/<repo>.md` (max ~60 lines) with:
1. Purpose in one sentence
2. Tech stack
3. Inbound / outbound interfaces (APIs, queues, topics, DBs, other repos it calls)
4. AI components found (LLM, agents, RAG, embeddings, guardrails, evals) with evidence
5. Build and deploy path (CI file -> artifact -> registry -> environment)
6. Challenges found - see "Mining challenges" below
7. Open questions

### Mining challenges ("what problems were faced and how were they solved")

Code shows the solution, history shows the problem. Look at:
- `git log --oneline --no-merges -n 300` and grep for: fix, revert, hotfix, workaround, timeout, retry, flaky, perf, memory, leak, race, migrate, security, CVE, rollback
- Commits that touch the same file many times (`git log --format= --name-only | sort | uniq -c | sort -rn | head`)
- ADRs, `TODO`/`FIXME`/`HACK` comments, retry/backoff/circuit-breaker code, feature flags, CHANGELOG
For each challenge write: **Problem -> Evidence -> Solution -> Trade-off**. Mark as [Inferred] unless a commit message or ADR states it.

## Phase 2 - Reduce (write the brief)

Read only the files in `_architecture_brief/repos/` and write `_architecture_brief/ARCHITECTURE_BRIEF.md`:

1. **Executive summary** - 5 bullets a manager can read in 30 seconds.
2. **High-Level Design** - Mermaid `flowchart` of all repos, data stores, external systems, and LLM providers; then 1 paragraph per major component.
3. **Key use-case scenarios** - 3 to 6 end-to-end flows. Each: trigger -> steps across repos -> output. Add a Mermaid `sequenceDiagram` for the top 2.
4. **AI / Agentic system design**
   - Where agents are used: agent name, goal, tools it can call, memory, who it hands off to, human-in-the-loop points
   - Agent pattern used (single agent, router, planner-executor, supervisor/multi-agent, ReAct, workflow-with-LLM-steps)
   - RAG pipeline: ingest -> chunk -> embed -> store -> retrieve -> rerank -> prompt -> answer -> cite
   - Embedding model, dimensions, vector store, refresh strategy
   - Guardrails table: input / retrieval / tool-use / output / runtime (rate limits, cost caps), each with evidence or "Not found"
   - Evaluation and observability of AI behaviour
5. **Deployment pipeline** - Mermaid diagram: commit -> CI checks -> build -> scan -> artifact -> deploy per environment -> approval gates -> rollback. Note IaC tooling and secrets handling.
6. **SDLC and Agentic Development Life Cycle mapping** - table with one row per stage and the repo/file evidence:
   Plan/Requirements | Design (ADRs) | Build | Test (unit/integration/contract) | Security (SAST/DAST/SCA/SBOM) | Release | Deploy | Operate/Monitor | Feedback
   plus agentic stages: Use-case & risk framing | Prompt/agent design | Tool & data grounding | Offline evals | Guardrails & red-teaming | Staged rollout | Online monitoring (drift, cost, hallucination) | Continuous improvement.
   Mark gaps clearly.
7. **Challenges and solutions** - top 5 to 10 across all repos, Problem -> Solution -> Trade-off -> Evidence.
8. **Risks and gaps** - what is missing or fragile, ranked by impact.
9. **Interview / presentation talking points** - 6 to 8 STAR-style bullets (Situation, Task, Action, Result) built only from confirmed findings.
10. **Open questions for the author** - things only a human knows (business impact, numbers, why a decision was made).

## Phase 3 - Interview prep (run after Phase 2, ideally in a fresh chat)

Read `ARCHITECTURE_BRIEF.md` and the per-repo notes. Write two files.
Use only [Confirmed] facts as answers; anything [Inferred] must be phrased as "I'd verify..." or listed for the author to fill in.

### A. `_architecture_brief/CHEATSHEET.md` (max 2 pages, glanceable)

1. **30-second pitch** - what the system does, for whom, why it matters.
2. **2-minute walkthrough** - the HLD told as a story: request comes in -> components -> data -> AI -> output.
3. **Whiteboard diagram** - one Mermaid diagram simple enough to redraw by hand in 60 seconds (max 10 boxes).
4. **Numbers to know** - repos, services, environments, deploy frequency, latency/throughput, token or cost figures, eval scores. Leave `<fill in>` where unknown; never invent numbers.
5. **Tech decisions table** - Decision | Alternatives considered | Why chosen | Trade-off accepted.
6. **AI building blocks in one line each** - LLM, agents, tools/MCP, RAG, embeddings, vector store, guardrails, evals, observability - each with a 10-year-old analogy and the repo where it lives.
7. **SDLC / ADLC one-liner per stage** - what tool or practice covers it.
8. **Top 5 STAR stories** - Situation, Task, Action, Result (with metric), Lesson learned. One per challenge.
9. **Red-flag answers to avoid** - claims that are not backed by evidence, or gaps an interviewer will spot (from "Risks and gaps").

### B. `_architecture_brief/INTERVIEW_DRILLDOWN.md`

Organize by topic: HLD & trade-offs | Agents & orchestration | RAG & embeddings | Guardrails & safety | Evals & observability | Deployment pipeline & IaC | Security & compliance (regulated environment) | Scaling, reliability & cost | Challenges & incidents | SDLC/ADLC & team process.

For each topic write 3 to 5 **scenario questions**, each grounded in this codebase (e.g. "Your retriever starts returning stale policy documents after a re-index - walk me through how you'd detect and fix it."). For each question give a **drill-down ladder** the way a senior interviewer pushes:

```
Q (Level 1 - what): ...
  Model answer (3-5 bullets, cite repo/file)
  -> Follow-up L2 (why / trade-off): ...
       Model answer
  -> Follow-up L3 (what breaks / at 10x scale / under audit): ...
       Model answer
  -> Follow-up L4 (what would you do differently now): ...
       Model answer
  Trap: the common wrong answer and why it is wrong
  First principle: the underlying idea in one sentence + real-world analogy a 10-year-old gets
```

Also include:
- **System-design curveballs** (5): change one constraint (10x users, new regulator rule, LLM provider outage, cost cut by 50%, on-prem only) and answer how the architecture adapts.
- **Debugging scenarios** (5): symptom -> hypotheses -> how to confirm -> fix -> prevention. Prefer real incidents found in git history.
- **Behavioural questions tied to the work** (5): conflict, ownership, failure, influence without authority, mentoring - answered with STAR from these repos where possible.
- **Questions to ask the interviewer** (5) that show depth in this domain.
- **Self-test section**: the same questions repeated without answers, so the author can practice out loud.

End with a **Gaps to fill before the interview** list: every `<fill in>` and [Inferred] item, so the author knows exactly what to recall or verify.

## Style

- Plain English first, jargon second. For each major concept add a one-line real-world analogy (e.g. "RAG is an open-book exam: the model looks up the textbook before answering").
- Prefer tables and diagrams over long paragraphs.
- Keep the final brief under ~6 pages. Detail stays in the per-repo notes.

## Finish

Report: number of repos analyzed, repos skipped and why, the 3 biggest open questions, and the paths of the files written (brief, cheatsheet, drill-down).
