# cloud-native-security-brain (4cs-secbrain)

A shared team knowledge base for cloud-native security, organized along two axes:

- **What** — a specific control from the [AWS Well-Architected Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html) (`SECxx-BPyy`, e.g. *SEC08-BP02 Enforce encryption at rest*).
- **Where** — one of the **4Cs** that name this repo:
  - **Code** — application source: SAST/DAST/SCA, dependencies, secrets in code, secure-coding, PR review
  - **Cloud** — cloud-provider services: AWS/GCP/Azure resources, IAM, KMS, networking, account/org guardrails
  - **Cluster** — orchestrator: Kubernetes (EKS/GKE/AKS), RBAC, NetworkPolicies, control-plane, service mesh
  - **Container** — image + runtime: Dockerfiles, base images, registries, image scanning, runtime hardening
  - *(plus `cross` for discussions that genuinely span ≥2 scopes)*

The same control is a very different conversation in each scope — *least privilege* in AWS IAM is not the same as in Kubernetes RBAC, container runtime caps, or application authorization. Keeping these separate preserves precision.

## Layout

```
memory/security/<domain>/<sec-id>/<scope>.md

# example
memory/security/data-protection/sec08-bp02/cloud.md
memory/security/data-protection/sec08-bp02/cluster.md
```

The seven `<domain>` folders match the AWS Security Pillar's best-practice areas:

| Folder | Covers |
| --- | --- |
| `security-foundations/` | SEC01 |
| `identity-and-access-management/` | SEC02, SEC03 |
| `detection/` | SEC04 |
| `infrastructure-protection/` | SEC05, SEC06 |
| `data-protection/` | SEC07, SEC08, SEC09 |
| `incident-response/` | SEC10 |
| `application-security/` | SEC11 |

Each file is append-only: every new discussion adds a `## YYYY-MM-DD — <short title>` section. Manual edits are welcome — see [`memory/security/README.md`](memory/security/README.md) for the entry format.

## How notes get added

1. **Via Claude Code.** This repo ships a project-local skill (`security-topic`) that runs inside [Claude Code](https://claude.com/claude-code). On every new topic it: (a) maps your question to a {control, scope} pair, (b) asks you to confirm, (c) surfaces any prior notes for that pair (and lists sibling-scope notes for the same control), (d) at the end offers to append a fresh summary to `memory/security/`. Conventions for Claude are documented in [`CLAUDE.md`](CLAUDE.md).
2. **By hand.** Open the relevant `<scope>.md`, add a new `## YYYY-MM-DD — <short title>` section. Commit.

## Using the skill — examples

Open Claude Code in this repo and use any of the trigger phrases below. The skill activates automatically; no slash command needed.

### Start a discussion on a specific topic

```
> let's discuss kubernetes logging
```

The skill spawns a read-only subagent to match the topic against the Security Pillar catalog, then confirms with you:

```
Proposed mapping:
  Domain     : Detection
  Control    : SEC04-BP01 — Configure service and application logging
  Scope      : cluster   (cues: kubernetes)
  Confidence : control=high, scope=high
Alternatives : SEC04-BP02, SEC04-BP03
```

After you confirm, it loads any prior notes from `memory/security/detection/sec04-bp01/cluster.md` and pins the discussion:

```
**Active control:** SEC04-BP01 — Configure service and application logging  (domain: detection, scope: cluster)
```

Every subsequent answer stays scoped to that pair until you switch topic.

### Discuss a whole domain

```
> review detection in general
```

```
> talk about data protection
```

Pins the *domain* rather than a single BP. Saves land under `memory/security/<domain>/_domain/<scope>.md`.

### Switch scope mid-discussion (no re-matching)

```
> switch scope to cloud
```

Re-emits the pin with the new scope and reloads prior notes for that scope. The control stays the same — useful when the same BP looks different from the cluster vs. cloud side (e.g. SEC03 least-privilege in IAM vs. Kubernetes RBAC).

### Switch to a new topic — closes the previous one first

```
> new topic: KMS key rotation
```

Before mapping the new topic, the skill drafts a 3–6 bullet summary of what was just discussed and asks where to save it:

- **Project memory** (shared, git-tracked) — appended to `memory/security/<domain>/<sec-id>/<scope>.md`
- **User memory** (private) — written to your personal `~/.claude/projects/.../memory/`
- **Edit summary first** or **Skip**

Then it runs the same map → confirm → load-prior → pin flow for the new topic.

### Other phrases that trigger the skill

- `"new discussion"`, `"next topic"`, `"change subject"`, `"switch topic"`
- `"I want to discuss X"`, `"start a discussion about X"`, `"talk about X"`
- `"discuss the whole X"`, `"X in general"`, `"review X"` (domain mode)

Follow-up turns inside an already-pinned discussion don't re-trigger the skill — just keep asking questions normally.

## Why this exists

Security knowledge usually evaporates: it lives in Slack threads, ticket comments, and people's heads. Forcing every discussion into a stable AWS-defined slot **and** a 4Cs scope turns one-off conversations into something colleagues can find, cite, and build on later — without confusing cloud advice with cluster advice.

## Getting around

- [`CLAUDE.md`](CLAUDE.md) — conventions for Claude Code (and humans) working in this repo.
- [`memory/security/README.md`](memory/security/README.md) — file format and tree organization.
- [`.claude/skills/security-topic/`](.claude/skills/security-topic/) — the skill itself, including the full Security Pillar catalog and scope-detection cues used for topic mapping.
