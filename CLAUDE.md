# cloud-security-mindset — Claude conventions

This repo is a shared knowledge base for cloud-native security work, organized along two axes:

1. **Control** — a specific best practice from the [AWS Well-Architected Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html) (`SECxx-BPyy`).
2. **Scope** — one of the **4Cs** of cloud-native security: **code, cloud, cluster, container** (plus `cross` for genuinely multi-scope discussions).

Every discussion is pinned to a `{control, scope}` pair. The skill at `.claude/skills/security-topic/` enforces this.

## The skill: `security-topic`

Activates whenever the user signals a new topic (e.g. "new topic", "let's discuss", "switch topic", or whole-domain phrasing like "review data protection"). Workflow:

1. **Closes the previous discussion** — drafts a 3–6 bullet summary and asks whether to save it. Destinations:
   - **Project memory** (shared, git-tracked) → `memory/security/<domain>/<sec-id>/<scope>.md` *(appended, never overwritten)*.
   - **User memory** (private) → the auto-memory directory named in the memory section of the system prompt.
2. **Maps the new topic** via an `Explore` subagent against the catalog at `.claude/skills/security-topic/reference/security-pillar.md`. The subagent returns both the BP control AND the detected scope (with confidence and cues).
3. **Confirms** both axes with the user. If scope is unknown or low-confidence, the skill explicitly asks before pinning.
4. **Loads prior notes** scope-first: primary file (`<domain>/<sec-id>/<scope>.md`), then sibling-scope files for the same control.
5. **Pins** the active `{control, scope}` pair with a line of the form:
   ```
   **Active control:** SEC08-BP02 — Enforce encryption at rest  (domain: data-protection, scope: cloud)
   ```
   This pin lasts until the next new-topic trigger.

Mid-discussion the user may say *"switch scope to <X>"* — the skill re-emits the pin with the new scope and reloads prior notes for that scope, without re-running the matcher.

## Memory layout

```
memory/security/
├── README.md
├── security-foundations/
├── identity-and-access-management/
├── detection/
├── infrastructure-protection/
├── data-protection/
├── incident-response/
└── application-security/
```

- One folder per control: `memory/security/<domain>/<sec-id>/`.
- One file per scope inside: `cloud.md`, `code.md`, `cluster.md`, `container.md`, `cross.md` — created on demand.
- Domain-wide notes live under `memory/security/<domain>/_domain/<scope>.md` (`_domain` is reserved).
- Entries are **appended** as dated sections (`## YYYY-MM-DD — <short title>`). Author comes from `git config user.name`, or `claude session` if unset.

## For Claude (working conventions)

- **Honour the pin.** If a `**Active control:**` or `**Active domain:**` line appears earlier in the transcript, all subsequent work stays scoped to that `{control, scope}` pair. Cite both briefly when making non-obvious recommendations (e.g. *"aligned with SEC03-BP02 / cluster"*).
- **Scope matters as much as control.** Don't mix advice from different scopes in one answer unless the pin is `scope: cross`. If the user's question wanders into another scope, surface the mismatch and offer to switch scope.
- **Don't re-invoke `security-topic` mid-discussion.** Only on a fresh new-topic signal — or on a scope-only switch ("switch scope to <X>"), which uses a shorter path.
- **Skills with catalog/reference files must read them via subagents.** Any skill in `.claude/skills/*/reference/` or `.claude/skills/*/references/` holds multi-entry reference data where only one entry ends up used. Route both the catalog scan and (where applicable) the materialization of the chosen entry through subagents so the unchosen entries never enter main context. Applies to `security-topic` (Security Pillar catalog → matcher subagent), `buddy` (personas catalog → summarizer + materializer subagents), `delivery-management` (framework references → delegation-protocol subagents), and to any future skill of the same shape.
- **The pin is transcript-only.** It doesn't survive new sessions. Durable knowledge belongs in `memory/security/<domain>/<sec-id>/<scope>.md`.
- **Project vs user memory.** Default save destination is project (shared) — the whole point of this repo is collaboration. Only fall back to user memory when the user explicitly chooses it or the content is personal/private.

## Agent persona (DISC "buddy")

At the very start of every new conversation, before your first substantive response, check for `agent_persona.md` in your auto-memory directory (the path is given in the "auto memory" section of your system prompt).

- **If it exists**, `Read` it once and adopt the described tone for the rest of the session. Do not announce the load; just start speaking in that voice.
- **If it does not exist**, invoke the `buddy` skill at `.claude/skills/buddy/` so the user can pick one. The skill writes the file and hands back the adopted persona block.

The persona modifies **tone and style only**. It never overrides these guardrails:

- No emojis unless the user explicitly asks for them.
- Cite `file_path:line_number` when referencing code.
- Confirm before destructive or hard-to-reverse actions.
- Prefer edits to new files; don't create documentation files unless asked.
- Honour any `**Active control:**` / `**Active domain:**` pin from `security-topic`.

The user can change persona at any time by saying "change buddy" / "смени бадди" — that re-invokes the `buddy` skill, which overwrites the persona file. To reset to no persona, delete `agent_persona.md`.

The subagent-only rule for `.claude/skills/*/reference/*.md` (see the working-conventions section above) applies here too: `personas.md` is read by the skill's subagents, never main context.
