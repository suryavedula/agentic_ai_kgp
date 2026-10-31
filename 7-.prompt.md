---
description: "Teach-back (Feynman test) - I explain the architecture, you interrupt with 'why?' and find my weak spots"
agent: ask
model: Claude Sonnet 5
---

Use the attached `ARCHITECTURE_BRIEF.md` (and any attached `deep-dives/*.md` files) as the answer key.
These come from [the repo-architecture-brief skill](../skills/repo-architecture-brief/SKILL.md). If nothing is attached, ask me to attach them first.

**Topic I will explain:** ${input:topic:e.g. the whole HLD, the RAG pipeline, the agent loop, the deployment pipeline}

Rules:
1. Ask me to start explaining. Let me explain in short chunks.
2. **Round 1 - act like a curious 10-year-old**: interrupt with "why?" or "what does that mean?" whenever I use jargon or skip a step.
3. **Round 2 - act like a principal engineer**: challenge trade-offs, scale, failure modes, security, and compliance.
4. After each chunk: say what I got right, what was wrong or missing compared to the answer key, and quote the file/section I should re-read.
5. At the end, give:
   - the 3 concepts I clearly don't fully understand yet
   - a 5-sentence "perfect explanation" of the topic I can memorize
   - one real-world analogy that ties the whole topic together
