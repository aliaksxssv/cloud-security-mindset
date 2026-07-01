---
name: doc-structure
description: Structure any documentation the user asks you to write or update around four questions — What, Why, How, Who — so docs stay easy to scan. Invoke when the user asks to write, update, add, or revise documentation — phrases like "write docs", "update the docs", "document this", "add documentation", "write a README", "write up X", "explain this in the docs", "add a doc for", "revise the doc". Applies to new docs and edits to existing ones; the wording of the four headings can flex to fit the material as long as each question is answered.
---

# Doc structure — What / Why / How / Who

When the user asks to write or update documentation, shape the content so a reader can scan it top-to-bottom and get oriented fast. Every doc answers four questions, in this order:

1. **What** — what this is / what we're doing. One or two sentences up front. No history, no throat-clearing.
2. **Why** — the motivation. Why it exists, what problem it solves, what breaks without it.
3. **How** — how it works or how to use it. Steps, commands, config, API — the operational core. This is usually the longest section.
4. **Who** — who it's for, who owns it, who to contact, or who is affected (permissions, audience, on-call).

## Rules

- **Answer all four, but don't pad.** If a section is genuinely one line, ship one line. Never invent a "Who" just to fill the slot — if ownership is unknown, say so or ask.
- **Wording can flex.** The literal headings can adapt to the material (e.g. *Overview / Rationale / Usage / Audience*, or *What it does / Why it matters / How to run it / Who maintains it*) as long as each of the four questions is clearly answered and the order holds.
- **Order is fixed.** What → Why → How → Who. Reader gets the "what" before the "why" before the mechanics.
- **Updates follow the same shape.** When revising an existing doc, reorganize toward this structure if it's close; don't force a full rewrite of a large doc unless asked — flag the mismatch and offer.
- **Match the surrounding docs.** Heading level, tone, and formatting should match the file or repo you're writing into.

## Guardrails (unchanged by this skill)

- Don't create new documentation files unless the user asked for one; prefer editing the existing doc.
- No emojis unless the user asks.
- Cite `file_path:line_number` when referencing code inside a doc.
- Honor any active `security-topic` pin.
