# cloud-security-mindset — Claude conventions

This repo is a shared knowledge base for cloud-native security work, organized along two axes:

1. **Control** — a specific best practice from the [AWS Well-Architected Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html) (`SECxx-BPyy`).
2. **Scope** — one of the **4Cs** of cloud-native security: **code, cloud, cluster, container** (plus `cross` for genuinely multi-scope discussions).

Every discussion is pinned to a `{control, scope}` pair. The skill at `.claude/skills/security-compass/` enforces this.

## The skill: `security-compass`

Activates whenever the user signals a new topic (e.g. "new topic", "let's discuss", "switch topic", or whole-domain phrasing like "review data protection") — and at conversation start whenever no pin exists yet (see the session-start gate below). Workflow:

1. **Closes the previous discussion** — drafts a 3–6 bullet summary and asks whether to save it. Destinations:
   - **Project memory** (shared, git-tracked) → `memory/security/<domain>/<sec-id>/<scope>.md` *(appended, never overwritten)*.
   - **User memory** (private) → the auto-memory directory named in the memory section of the system prompt.
2. **Maps the new topic** via an `Explore` subagent against the catalog at `.claude/skills/security-compass/reference/security-pillar.md`. The subagent returns both the BP control AND the detected scope (with confidence and cues).
3. **Confirms** both axes with the user. If scope is unknown or low-confidence, the skill explicitly asks before pinning.
4. **Loads prior notes** scope-first: primary file (`<domain>/<sec-id>/<scope>.md`), then sibling-scope files for the same control — and, when a doc index exists under `memory/docs/index/`, pulls matching external links via subagents (Step 4b).
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
- The delivery self-assessment lives at `memory/delivery/self-assessment.md` — current-state facts (company & external compliance, security team size & skills, budget posture) gathered by the `delivery-management` skill at the first conversation and reused by its triangle intake. Unlike the discussion logs above, its sections are **updated in place**, each ending with `_Last updated: YYYY-MM-DD_`; skipped questions are tracked under `## Open questions` and re-offered later. Created lazily on first save.
- External doc context lives under `memory/docs/`: `sources.md` (registered HTTP sources, access hints — never secrets — last-scan dates, scan stats) and `index/<domain-slug>.md` (links/anchors mapped to Security Pillar controls, consumed by `security-compass` Step 4b). Maintained by the `doc-context` skill; index files are read only by subagents. Created lazily.

## For Claude (working conventions)

- **Run the session-start gate first.** In a fresh session — or any transcript with no pin — don't start substantive work until the checks in the session-start gate section below have resolved: persona loaded/picked, external doc sources registered or staleness-checked (skippable), `{control, scope}` pinned (or the user explicitly chose to proceed without a control), and — on the very first conversation only — the delivery self-assessment offered (every answer skippable).
- **Honour the pin.** If a `**Active control:**` or `**Active domain:**` line appears earlier in the transcript, all subsequent work stays scoped to that `{control, scope}` pair. Cite both briefly when making non-obvious recommendations (e.g. *"aligned with SEC03-BP02 / cluster"*).
- **Scope matters as much as control.** Don't mix advice from different scopes in one answer unless the pin is `scope: cross`. If the user's question wanders into another scope, surface the mismatch and offer to switch scope.
- **Don't re-invoke `security-compass` mid-discussion.** Only on a fresh new-topic signal — or on a scope-only switch ("switch scope to <X>"), which uses a shorter path.
- **Skills with catalog/reference files must read them via subagents.** Any skill in `.claude/skills/*/reference/` or `.claude/skills/*/references/` holds multi-entry reference data where only one entry ends up used. Route both the catalog scan and (where applicable) the materialization of the chosen entry through subagents so the unchosen entries never enter main context. Applies to `security-compass` (Security Pillar catalog → matcher subagent), `disc-persona` (personas catalog → summarizer + materializer subagents), `delivery-management` (framework references → delegation-protocol subagents), `doc-context` (source scans and `memory/docs/index/` lookups → scan/lookup subagents that map content against the Security Pillar catalog), and to any future skill of the same shape.
- **The pin is transcript-only.** It doesn't survive new sessions. Durable knowledge belongs in `memory/security/<domain>/<sec-id>/<scope>.md`.
- **Project vs user memory.** Default save destination is project (shared) — the whole point of this repo is collaboration. Only fall back to user memory when the user explicitly chooses it or the content is personal/private.

## Session-start gate (persona + docs + compass + assessment)

At the very start of every new conversation, before your first substantive response, run the checks below **in order** — tone, then external context sources, then topic, then delivery context. No discussion continues until they have resolved.

### 1. Persona check (DISC "buddy")

Check for `agent_persona.md` in your auto-memory directory (the path is given in the "auto memory" section of your system prompt).

- **If it exists**, `Read` it once and adopt the described tone for the rest of the session. Do not announce the load; just start speaking in that voice.
- **If it does not exist**, invoke the `disc-persona` skill at `.claude/skills/disc-persona/` so the user can pick one. The skill writes the file and hands back the adopted persona block.

The persona modifies **tone and style only**. It never overrides these guardrails:

- No emojis unless the user explicitly asks for them.
- Cite `file_path:line_number` when referencing code.
- Confirm before destructive or hard-to-reverse actions.
- Prefer edits to new files; don't create documentation files unless asked.
- Honour any `**Active control:**` / `**Active domain:**` pin from `security-compass`.

The user can change persona at any time by saying "change buddy" / "смени бадди" — those phrases trigger the `disc-persona` skill, which overwrites the persona file. To reset to no persona, delete `agent_persona.md`.

The subagent-only rule for `.claude/skills/*/reference/*.md` (see the working-conventions section above) applies here too: `personas.md` is read by the skill's subagents, never main context.

### 2. External docs check (doc-context)

Check whether `memory/docs/sources.md` exists:

- **If it does not exist** (very first conversation), invoke the `doc-context` skill at `.claude/skills/doc-context/` so the user can register HTTP-based documentation sources (Confluence, Google Docs, wikis, policy portals describing the current state of security controls or security policies) — or skip. Registered sources are scanned by **background subagents** that map found sections/anchors to Security Pillar domains and controls in `memory/docs/index/`; the conversation continues while they run.
- **If it exists**, read it (it is small) and compare each source's `Last scan` date with today. If any is older than **7 days**, offer a rescan — the skill suggests which sources to prioritize based on the stats recorded at scan time. Never rescan without asking.

This check never blocks: skipping registration or a rescan is always valid.

### 3. Compass check ({control, scope} pin)

Next: if no `**Active control:**` / `**Active domain:**` line exists anywhere in the transcript, invoke the `security-compass` skill **before answering — regardless of topic**, including repo-maintenance requests. Treat the user's first substantive message as the topic statement.

The discussion continues only once the skill has emitted the pin — or the user has explicitly chosen **"Proceed without a control"** (the skill's no-match step). That option is the sanctioned exit for non-security work: the clarification exchange still happens, the user decides, and the session is unblocked with an explicit *no pin* note.

### 4. Delivery self-assessment (current state & constraints)

After the pin is resolved, check whether `memory/delivery/self-assessment.md` exists:

- **If it does not exist** (very first conversation), invoke the `delivery-management` skill's self-assessment flow: skippable questions about company & external compliance, security team size & skills, and budget posture. Answers are persisted to that file via a subagent; every question can be skipped.
- **If it exists**, have an `Explore` subagent return its ≤3-line summary plus the `## Open questions` list — never load the full file into main context. If any questions are still open, surface them in a single line (*"2 assessment questions still open — say 'resume assessment' whenever"*) and move on. Don't re-ask.

Unlike checks 1 and 3 this gate never blocks: skipping every question is a valid outcome. Skipped dimensions are re-offered later — on "resume assessment", or one-at-a-time when a delivery decision actually needs the missing answer.
