---
description: "Architecture brief - Phase 2: combine repo notes into ARCHITECTURE_BRIEF.md"
agent: agent
model: Claude Opus 5.5
---

Read and follow [the repo-architecture-brief skill](../skills/repo-architecture-brief/SKILL.md).

Run **Phase 2 only**.

- Input: only `_architecture_brief/00-inventory.md` and `_architecture_brief/repos/*.md`.
- Open source code only to verify a specific claim that looks wrong or unclear.
- Output: `_architecture_brief/ARCHITECTURE_BRIEF.md` with all 10 sections from the skill, including Mermaid diagrams.
- Keep [Confirmed] / [Inferred] / [Unknown] tags. Never invent numbers or components.

When done: summarize the 3 biggest open questions. Do NOT start Phase 3.
