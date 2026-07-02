# Security knowledge base

This folder is a living team knowledge base of security discussions and decisions, organized along two axes:

1. **What** — a specific control from the [AWS Well-Architected Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html) (one of ~62 best practices like *SEC08-BP02 Enforce encryption at rest*).
2. **Where** — one of the **4Cs** scopes that give this repo its name: **code, cloud, cluster, container** (or `cross` when a discussion genuinely spans multiple).

The same control is a very different conversation in each scope — *least privilege* in AWS IAM is not the same as in Kubernetes RBAC, container runtime caps, or application-level authorization. Keeping them in separate files preserves precision.

## Layout

```
memory/security/<domain>/<sec-id>/<scope>.md
```

- `<domain>` — one of the seven Security Pillar areas (folders here).
- `<sec-id>` — lowercased best-practice id, e.g. `sec08-bp02`.
- `<scope>` — `code` | `cloud` | `cluster` | `container` | `cross`.

Example:

```
memory/security/data-protection/sec08-bp02/
├── cloud.md
├── code.md
├── cluster.md
├── container.md
└── cross.md
```

Domain-wide notes (when a discussion covers a whole domain rather than one BP) live under:

```
memory/security/<domain>/_domain/<scope>.md
```

`_domain` is reserved — never used as a `<sec-id>` slug.

## File format

Each scope file starts with a stable header (written once on first save):

```markdown
# SEC08-BP02 — Enforce encryption at rest  ·  scope: cloud
AWS doc: https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/sec_protect_data_rest_encrypt.html
```

Entries are **appended** as dated sections — never overwritten:

```markdown
## 2026-06-30 — Default encryption rollout plan
**Author:** Alex K
- Switched all S3 buckets to SSE-S3 by default…
**Why:** compliance requirement from review on 2026-06-15.
**Decisions / outcomes:** …
```

Manual edits are welcome — stick to this format so the skill can keep parsing the file.

## How entries land here

- **Automatically**: via the `security-compass` Claude Code skill in `.claude/skills/security-compass/`. When a discussion wraps up, you're asked whether to save the summary into project (shared) or user (private) memory. *Project* writes here, under the right `<domain>/<sec-id>/<scope>.md` path.
- **Manually**: edit the file directly.

## Why this exists

So colleagues (and future you) don't have to re-derive context every time a security topic comes up. Each entry is short, dated, and pinned to one control × scope — easy to skim, easy to cite, organized exactly the way AWS organizes its own guidance.
