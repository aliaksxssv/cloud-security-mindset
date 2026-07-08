---
name: doc-context
description: Fetch extra security context from custom HTTP-based documentation — Confluence, Google Docs, wikis, policy portals — wherever the current state of security controls or a security policy with relevant requirements is described. Maintains a source registry (memory/docs/sources.md) and a per-domain link index (memory/docs/index/<domain-slug>.md) that security-compass consults on every new topic. Invoke when the user says "register docs", "add doc source", "scan docs", "rescan docs", "update doc index", or names a Confluence/Google Docs/policy URL as a context source — and at conversation start via CLAUDE.md handoff, on the very first conversation (no sources.md yet → offer registration + initial scan) or when any registered source's last scan is older than 7 days (offer a rescan with data-driven suggestions). Scans run in background subagents; registration and rescans are always skippable.
---

# External doc context

Team documentation (Confluence, Google Docs, wikis, policy portals) often holds the *actual* state of security controls and the policies that set requirements — context no catalog can provide. This skill registers those HTTP-based sources, scans them via subagents, and indexes their links/anchors against Security Pillar domains and controls so that `security-compass` can pull the matching external context whenever a topic is pinned.

## Files this skill maintains

- **Registry** — `memory/docs/sources.md` (project memory, git-tracked; small, safe for main context):

  ```markdown
  # External doc sources

  ## <source-name>
  - URL: <base url>
  - Type: confluence | google-docs | wiki | other
  - Auth: none | <access hint, e.g. "curl with $CONFLUENCE_TOKEN"> — NEVER the secret itself
  - Last scan: YYYY-MM-DD
  - Stats: <N pages, M entries — data-protection (5), detection (2), …>
  ```

- **Index** — `memory/docs/index/<domain-slug>.md`, one file per Security Pillar domain (same seven slugs as `memory/security/`), read only by subagents:

  ```markdown
  # External doc index — <Domain name>

  ## SECxx-BPyy
  - <section title> — <url#anchor> — <one-line summary> (source: <source-name>, scanned YYYY-MM-DD)

  ## _domain
  - <entries that map to the domain but no single control>
  ```

Both are created lazily. Entries are **replaced per source+url on rescan** (current state, not a log).

## When to invoke

- **CLAUDE.md gate handoff, first conversation** — `memory/docs/sources.md` does not exist: offer registration + initial scan.
- **CLAUDE.md gate handoff, stale scan** — any source's `Last scan` is older than 7 days: offer a rescan (Step 3). Never rescan without asking.
- **On demand** — "register docs", "add doc source", "scan docs", "rescan docs", "update doc index", or the user pastes a docs URL as a context source.

This skill never blocks: skipping registration or a rescan is always a valid outcome.

## Workflow

### Step 1 — Register sources

`AskUserQuestion`: does the team have HTTP-based docs describing the current state of security controls or security policies? Options: **Add sources now** / **Skip for now**. On "add", collect per source (free-text is fine): name, base URL, type, and — for authenticated sources — an access hint (e.g. the name of an env var holding a token, or "reachable from this machine"). Write the registry entries. **Never store credentials** in the registry; it is git-tracked.

### Step 2 — Initial scan (background subagent)

For each registered source spawn a `general-purpose` subagent with `run_in_background: true` so the conversation continues while it works. Prompt template (substitute placeholders):

> Scan external security documentation. Source: `<name>` — `<base URL>` (type: `<type>`; access: `<auth hint>`).
>
> 1. Fetch the entry page (WebFetch for public URLs; Bash `curl` per the access hint for authenticated ones). Follow same-site/same-space links up to depth 2, max 30 pages.
> 2. Identify sections that describe the current state of security controls, security policies, or security requirements. Ignore everything else.
> 3. Read `<REPO_ROOT>/.claude/skills/security-compass/reference/security-pillar.md` and map each relevant section to a domain slug (and a SECxx-BPyy control where the match is clear; use the `_domain` section otherwise).
> 4. Update `<REPO_ROOT>/memory/docs/index/<domain-slug>.md` for every mapped domain (create folders/files as needed): one bullet per section under the control heading — `- <section title> — <url#anchor> — <one-line summary> (source: <name>, scanned <today>)`. Replace existing bullets from the same source+url; do not duplicate.
> 5. Return only: pages fetched, entries written per domain, and any URLs that failed (with the error).

When a scan finishes, update the source's `Last scan` and `Stats` lines in the registry from the subagent's report, and tell the user in one line what was indexed. If the current discussion is already pinned, offer to run the compass Step 4b lookup now that the index exists.

### Step 3 — Staleness check & rescan suggestions

When the gate (or the user) triggers a rescan review:

1. Read `memory/docs/sources.md` (main context — it is small). List sources whose `Last scan` is older than 7 days.
2. Suggest what to rescan **based on the recorded stats**: prioritize sources whose entries feed the domains the team actually uses (folders present under `memory/security/`) and those with the most indexed entries; deprioritize sources that yielded little.
3. `AskUserQuestion`:
   - **Rescan all stale sources** *(Recommended)*
   - **Rescan the suggested subset** — name them and why (e.g. *"policy-wiki — feeds data-protection, your most active domain"*)
   - **Skip for now** — note that stale links may 404 or mislead
4. Rescans reuse the Step 2 subagent template (background). Update registry lines on completion.

### Step 4 — How the index is consumed

`security-compass` Step 4b does the lookup on every newly pinned topic: an `Explore` subagent reads `memory/docs/index/<domain-slug>.md` for the pinned control (all sections in domain mode), a second subagent fetches the matched links/anchors, and a ≤10-line **External context** digest is surfaced next to **Prior context**. This skill only maintains the data; it does not run the lookup itself.

## Notes

- **Secrets never touch git.** The registry stores access *hints* (env var names, "VPN required"), never tokens or cookies. Fetch failures due to auth are reported to the user, who runs the scan when access is available.
- **Bounded crawl.** Depth 2, max 30 pages per source per scan — external docs are context, not the knowledge base itself. Durable conclusions still belong in `memory/security/`.
- **Index files are subagent-only** — same context-economy discipline as every catalog in this repo. The registry (`sources.md`) is the one file small enough for main context.
- **Rescan cadence is 7 days**, checked only at session start; no background timers.
