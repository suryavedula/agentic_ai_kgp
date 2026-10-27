---
description: "Architecture brief - Phase 0+1: inventory every repo and write one note per repo"
agent: agent
model: Claude Sonnet 5
---

Read and follow [the repo-architecture-brief skill](../skills/repo-architecture-brief/SKILL.md).

Run **Phase 0 and Phase 1 only** for every repository in this workspace folder.

- Process repos one at a time. Write each `_architecture_brief/repos/<repo>.md` note before starting the next repo.
- Keep each note under ~60 lines. Do not read whole large files - search and read excerpts.
- Read-only: do not modify any repo. Only write inside `_architecture_brief/`.
- Only run read-only git commands (`git log`, `git ls-files`, `git shortlog`).
- If you run out of steps, stop cleanly and say which repos are still left, so the next run can say "continue".

${input:scope:Leave empty for all repos, or list repo names to do only a batch}

When done: list repos analyzed, repos skipped and why. Do NOT start Phase 2.
