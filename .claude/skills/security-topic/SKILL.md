---
name: security-topic
description: Anchor every discussion to BOTH (a) a specific AWS Well-Architected Security Pillar control (SECxx-BPyy) and (b) one of the 4Cs scopes — code, cloud, cluster, container (or cross). Invoke when the user signals a new topic — phrases like "new topic", "new discussion", "let's discuss", "I want to discuss", "talk about", "switch topic", "next topic", "start a discussion about", "change subject", or whole-domain phrasing like "discuss the whole <domain>", "review <domain>", "<domain> in general". The skill (1) closes the prior pinned discussion with a save prompt that lets the user choose project (shared via git) vs user (private) memory, (2) spawns an Explore subagent that maps the topic to a control AND detects scope from cues, (3) asks the user to confirm both axes, (4) loads prior notes — same control + same scope first, then sibling scopes, (5) pins {control, scope} for the rest of the discussion. Mid-discussion the user can say "switch scope to <X>" to re-pin scope without re-mapping the control.
---

# AWS Security Topic Anchor (control × scope)

This skill keeps every discussion in this repo anchored to one **control × scope** pair — a specific AWS Well-Architected Security Pillar best practice AND one of the 4Cs scopes (code, cloud, cluster, container, or cross). The pin lasts until the next new-topic trigger.

## When to invoke

Trigger when the user opens a new line of work:

- "new topic / new discussion"
- "let's discuss …", "I want to discuss …", "talk about …"
- "switch topic", "next topic", "change subject"
- "start a discussion about …"
- whole-domain phrasing: "discuss the whole <domain>", "review <domain>", "<domain> in general", "everything about <domain>"

Also handle the **scope-only switch** mid-discussion: "switch scope to <code|cloud|cluster|container|cross>". In that case skip Steps 1–3 and 5; just re-emit the pin with the new scope and re-run Step 4.

Do **not** re-invoke from scratch for follow-up turns inside an already-pinned discussion — just keep working under the active pin.

## Files this skill uses

- Catalog (read by the matcher subagent, not by main context):
  `.claude/skills/security-topic/reference/security-pillar.md`
- Project (shared, git-tracked) memory tree:
  `memory/security/<domain-slug>/<sec-id-lowercased>/<scope>.md`
- User (private) memory directory:
  `/Users/aliaksxssv/.claude/projects/-Users-aliaksxssv-Documents-github-4cs-secbrain/memory/`

## Workflow

### Step 1 — Close out the previous discussion (if one is pinned)

Look at the transcript for a pin line of either form:
- `**Active control:** SECxx-BPyy — <title>  (domain: <domain-slug>, scope: <scope>)`
- `**Active domain:** <Domain>  (slug: <domain-slug>, scope: <scope>)`

If present:

1. Draft a 3–6 bullet summary of what was discussed/decided under that pin.
2. `AskUserQuestion`:
   - **Save (shared with colleagues — project memory)** *(Recommended)*
   - **Save privately (user memory)**
   - **Edit summary first, then save**
   - **Skip — don't save**
3. **Project save**:
   - Path: `memory/security/<domain-slug>/<sec-id-lowercased>/<scope>.md` (control mode) or `memory/security/<domain-slug>/_domain/<scope>.md` (domain mode — domain-level notes live in a `_domain` folder alongside per-control folders to avoid colliding with any future `<sec-id>` folder named the same).
   - Use Bash `mkdir -p` to ensure both domain and control folders exist.
   - If the file doesn't exist, write a header on first save:
     ```markdown
     # SECxx-BPyy — <control title>  ·  scope: <scope>
     AWS doc: <link from catalog>

     ```
     (For `_domain` files: `# <Domain name> — domain-wide notes · scope: <scope>`.)
   - **Append** a new dated section (never overwrite):
     ```markdown
     ## <YYYY-MM-DD> — <short title>
     **Author:** <git user.name if available, else "claude session">
     <3–6 bullet summary>
     **Why:** <motivation>
     **Decisions / outcomes:** <bullets>
     ```
   - Author from Bash `git config user.name`; fall back to `claude session` if empty.
4. **User save**: write to `/Users/aliaksxssv/.claude/projects/-Users-aliaksxssv-Documents-github-4cs-secbrain/memory/discussion-<sec-id-lowercased>-<scope>-<short-slug>.md` with the global memory frontmatter (`type: project`); update `MEMORY.md` with a one-line index entry.
5. **Edit**: present the draft; user revises; then re-ask save destination.
6. **Skip**: discard and proceed.

If no pin line exists, skip Step 1 entirely.

### Step 2 — Map the new topic via subagent

Spawn an `Explore` subagent (read-only, keeps catalog out of main context):

- **description**: `Map topic to Security Pillar control + 4Cs scope`
- **subagent_type**: `Explore`
- **prompt** (verbatim):
  > Read `/Users/aliaksxssv/Documents/github/4cs-secbrain/.claude/skills/security-topic/reference/security-pillar.md` and follow the "Matcher instructions" section at the bottom.
  >
  > User topic: `<paste the user's exact topic statement>`
  >
  > Return only the structured output described in that section — no preamble, no extra prose.

Expected output:

```
mode: control | domain
pillar: Security
domain: <name>
domain_slug: <slug>
sec_question: SECxx                       # empty if mode=domain
control_id: SECxx-BPyy                    # empty if mode=domain
control_title: <title>                    # empty if mode=domain
scope: code | cloud | cluster | container | cross | unknown
scope_confidence: high | medium | low
scope_cues: <cues>                        # empty if scope=unknown
confidence: high|medium|low
alternatives: SECaa-BPbb, SECcc-BPdd
reasoning: <≤2 sentences>
```

### Step 3 — Confirm with the user

Present both axes compactly:

```
Proposed mapping:
  Domain     : <domain>
  Control    : SECxx-BPyy — <title>          (control mode only)
  Scope      : <scope>   (cues: <scope_cues>)
  Why        : <reasoning>
  Confidence : control=<level>, scope=<level>
Alternatives: SECaa-BPbb, SECcc-BPdd        (control mode only)
```

Then `AskUserQuestion`:
- **Confirm both** *(Recommended when both confidences are high)*
- **Change scope** → second `AskUserQuestion` with `code / cloud / cluster / container / cross`
- **Use alternative control** (if any alternatives) — then ask the scope question separately
- **Describe a better fit** → user types refinement; re-run Step 2

**Rule**: if `scope_confidence: low` OR `scope: unknown`, do NOT default-confirm. Always show the scope picker explicitly before pinning.

For `mode: domain`: same flow but no control row, no alternatives; the scope question still applies.

### Step 4 — Load prior notes (scope-first)

After confirmation, inspect the filesystem (read-only Bash):

**Control mode**:
1. Primary file: `memory/security/<domain-slug>/<sec-id-lowercased>/<scope>.md`. If it exists:
   - Count `^## ` sections (e.g. `grep -c '^## ' <path>`).
   - Grab dates (e.g. `grep '^## ' <path>`).
2. Sibling files: `ls memory/security/<domain-slug>/<sec-id-lowercased>/ 2>/dev/null`. List other-scope files with entry counts (e.g. `cloud.md (3 entries)`).
3. `AskUserQuestion` (build options dynamically based on what exists):
   - **Load primary all entries** *(Recommended when ≤2 exist)*
   - **Load primary latest only** *(Recommended when ≥3 exist)*
   - **Load primary latest 3**
   - **Load primary + all siblings** (if siblings exist)
   - **Skip — don't load any**
4. Read chosen entries with `Read`; surface them as a compact **Prior context** block before normal work begins.

**Domain mode**:
1. `ls memory/security/<domain-slug>/` and for each control folder count scope files inside.
2. `AskUserQuestion`:
   - **Load every `<scope>.md` in this domain** (matches the pinned scope across all controls)
   - **Pick a subset of controls to load**
   - **Skip**
3. Read selected files; surface as **Prior context**.

If nothing exists at any of those paths, say so briefly and continue.

### Step 5 — No-match handling

If `confidence: low` or the subagent can't pick a usable BP/domain:

1. Surface the closest pillar/domain.
2. `AskUserQuestion`:
   - **Force-map to closest BP** (list 1–2 from `alternatives`)
   - **Proceed without a control** — explicitly note *no SEC control pinned for this discussion*; skip Step 4 and Step 6 pinning
   - **Rephrase the topic** → re-run Step 2

### Step 6 — Pin {control, scope}

After the user confirms, emit one of these as the last line before handing back to normal work:

Control mode:
```
**Active control:** SECxx-BPyy — <title>  (domain: <domain-slug>, scope: <scope>)
```

Domain mode:
```
**Active domain:** <Domain name>  (slug: <domain-slug>, scope: <scope>)
```

The trailing slugs carry forward so the next Step 1 save knows the exact path without re-running the matcher.

For the rest of this discussion:
- Keep the {control, scope} pair in mind. Cite briefly when making non-obvious recommendations (e.g. *"aligned with SEC03-BP02 / cluster"*).
- If the user says **"switch scope to <X>"** mid-discussion: do not re-run the matcher; just re-emit the pin line with the new scope and re-run Step 4 (prior-notes load) for the new scope.
- Do **not** otherwise re-invoke this skill unless the user signals a new topic per the trigger list above.

## Notes

- Catalog edits (new aliases, new scope cues) belong in `reference/security-pillar.md`, not in this `SKILL.md`.
- Project memory under `memory/security/` is **git-tracked and shared**. User memory under `~/.claude/projects/.../memory/` is **private to this machine**.
- Append, never overwrite. Lazy-create folders on save.
- `cross` is a real scope value, not a fallback for "unsure". Use `unknown` (and ask the user) when there are no scope cues at all.
- `_domain` is reserved as a folder name under each domain for domain-mode notes; do not use it as a `<sec-id>` slug.
