---
description: "Architecture as a story - follow one request on its journey through the system, told with analogies, saved to _architecture_brief/STORY.md"
agent: agent
model: Claude Opus 5.5
---

Follow the **Ground rules** of [the repo-architecture-brief skill](../skills/repo-architecture-brief/SKILL.md):
no invented components, no secrets, read-only on repos.

**Source material:** `_architecture_brief/ARCHITECTURE_BRIEF.md`, `_architecture_brief/repos/*.md`,
and any files in `_architecture_brief/deep-dives/`. Open source code only to check a detail. If the brief does not exist yet, say so and stop.

**Story focus:** ${input:focus:e.g. the whole system, one incident ticket's journey, the RAG pipeline, the deployment pipeline - leave empty for the whole system}

Write the architecture as a **story** and save it to `_architecture_brief/STORY.md`
(or `STORY-<focus>.md` if a focus is given). Write it as continuous narrative prose, not bullet lists.

## How to tell it

- **One hero:** follow a single request (a user question, a ticket, an event) from the moment it is born to the moment it gets its answer.
- **Characters = components.** Give each real component a character role with a consistent analogy
  (e.g. API gateway = the front desk, queue = the waiting room, vector store = the library index, LLM = the expert consultant,
  agent = the detective who decides which clues to follow, tools = the detective's phone calls, guardrails = the security guards and editors,
  CI/CD = the factory that builds and inspects new parts). Pick analogies that fit THIS system; keep each one for the whole story.
- The first time a character appears, put its real name in brackets once, e.g. "the librarian (`retrieval-service`, Azure AI Search)".
- Explain at the level a curious 10-year-old can follow, but stay technically accurate - an expert should nod, not wince.
- Only describe what the source material confirms. If a step is [Inferred], make the narrator say so ("nobody wrote down why, but it seems...").

## Chapters

1. **The world** - what this system is for, who uses it, and the problem that existed before it (why it had to be built).
2. **The journey** - the hero's trip through every component, in order: what happens at each stop, what it carries, who it meets, how decisions are made. This is the longest chapter.
3. **The thinking part** - inside the agent / LLM: how context is gathered (RAG, embeddings), how the agent decides which tool to use, how it remembers, when a human steps in.
4. **The guardians** - the guardrails, security, audit trail and compliance checks, and what each one is protecting against.
5. **Plot twists** - 3 to 5 things that go wrong (from the failure modes and the real challenges found in git history) and how the system, or the engineer, saved the day.
6. **How the world is built** - the deployment pipeline and SDLC / Agentic Development Life Cycle as the story of how a new part is designed, built, tested, inspected and delivered.
7. **Epilogue** - what was learned, and what the next chapter (future improvements) would be.

## After the story, add three short appendices

- **Cast list** - table: story character | real component | repo / file | one-line real job.
- **First principles behind the story** - 5 to 8 one-sentence principles the story illustrates (e.g. "retrieve before you generate, so the model answers from facts, not memory").
- **The 60-second interview version** - the same story retold in about 150 words, ready to say out loud when an interviewer asks "walk me through your architecture".

When done: say the file path and suggest which chapter to practise first with `/brief-7-teach-back`.
