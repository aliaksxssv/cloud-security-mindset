# AWS Well-Architected Security Pillar — Control Catalog

Authoritative source: https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html

This file is loaded by the `security-topic` skill's matcher subagent to map a user topic to one specific control. Keep the structure stable so the matcher can rely on it.

Hierarchy used throughout this catalog:

```
Pillar  : Security (one of six AWS Well-Architected pillars)
  Domain      : one of seven best-practice areas
    SEC question: SEC01–SEC11
      Control   : SECxx-BPyy  (the unit you map a topic to)
```

---

## Design principles (7)

1. **Implement a strong identity foundation** — least privilege, separation of duties, centralized identity, eliminate long-term static credentials.
2. **Maintain traceability** — monitor, alert, audit changes in real time; integrate logs/metrics with automated investigation.
3. **Apply security at all layers** — defense in depth across edge, VPC, LB, instances, OS, app, code.
4. **Automate security best practices** — controls as code in version control; auto-scaling secure architectures.
5. **Protect data in transit and at rest** — classify by sensitivity; use encryption, tokenization, access control.
6. **Keep people away from data** — reduce direct/manual access to sensitive data.
7. **Prepare for security events** — incident plans, simulations, automated detection/investigation/recovery.

---

## Domains (7) and their SEC questions

| Domain | SEC questions | Slug (used in memory paths) |
| --- | --- | --- |
| Security foundations | SEC01 | `security-foundations` |
| Identity and access management | SEC02 (Identity mgmt), SEC03 (Permissions mgmt) | `identity-and-access-management` |
| Detection | SEC04 | `detection` |
| Infrastructure protection | SEC05 (Networks), SEC06 (Compute) | `infrastructure-protection` |
| Data protection | SEC07 (Classification), SEC08 (At rest), SEC09 (In transit) | `data-protection` |
| Incident response | SEC10 | `incident-response` |
| Application security | SEC11 | `application-security` |

**Slugs are canonical** — the matcher subagent must return exactly the slug from this table. Memory paths derive directly from it: `memory/security/<slug>/<sec-id-lowercased>/<scope>.md`.

---

## Scopes (the 4Cs — second mapping axis)

Every discussion is pinned to both a control AND a scope. The four scopes are the units that give the repo its name: **code, cloud, cluster, container** — plus `cross` when a discussion genuinely spans ≥2 of them.

| Scope | Slug | Covers |
| --- | --- | --- |
| Code | `code` | Application source: SAST/DAST/SCA, dependencies, secrets in code, secure-coding, code review, pipeline tests |
| Cloud | `cloud` | Cloud provider services: AWS/GCP/Azure resources, IAM, KMS, networking, account/org guardrails |
| Cluster | `cluster` | Orchestrator: Kubernetes (EKS/GKE/AKS), RBAC, NetworkPolicies, control-plane, service mesh |
| Container | `container` | Image + runtime: Dockerfiles, base images, registries, image scanning, runtime hardening |
| Cross | `cross` | Discussions that genuinely span ≥2 scopes (e.g. "least privilege everywhere") |

`unknown` is an internal matcher value used only when no cues fire; the skill asks the user before saving — never lands on disk.

### Scope detection cues

| Scope | Cues (case-insensitive substring/word matches) |
| --- | --- |
| `code` | SAST, DAST, SCA, secret scanning, gitleaks, trufflehog, OWASP, dependency, library, package, Dependabot, Renovate, language-specific names (Python, Go, Java, Node, …), code review, PR review, pull request, source code, repo, codebase, secure coding, lint |
| `cloud` | S3, EC2, IAM, KMS, Lambda, RDS, DynamoDB, CloudTrail, GuardDuty, Config, Security Hub, VPC, subnet, account, region, Organizations, SCP, RCP, GCS, BigQuery, Azure Blob, Storage Account, ARM, managed service, "in the cloud", landing zone, Control Tower |
| `cluster` | Kubernetes, k8s, EKS, GKE, AKS, kubectl, pod, namespace, deployment, statefulset, daemonset, kube-apiserver, etcd, kubelet, kube-proxy, k8s RBAC, NetworkPolicy, PodSecurity, OPA, Gatekeeper, Kyverno, Helm, service mesh, Istio, Linkerd, mTLS in cluster |
| `container` | Docker, image, Dockerfile, OCI, runtime, containerd, CRI-O, registry, ECR, GCR, ACR, Harbor, image scan, Trivy, Grype, base image, distroless, multi-stage build, gVisor, Kata, seccomp, capabilities, AppArmor, container escape |
| `cross` | "everywhere", "in all scopes", "across code/cloud/cluster/container", "all 4cs", "every layer", explicit naming of ≥2 scopes |

A cue is a match if it appears as a word/substring in the user's topic statement (case-insensitive). Ties broken by which scope had more cues.

---

## Full control catalog

Each control entry follows this shape so the matcher can parse it:

```
- id: SECxx-BPyy
  title: <official title>
  one_liner: <≤140 chars summary>
  keywords: [comma, separated, hints]
```

### Domain: Security foundations

#### SEC01 — How do you securely operate your workload?
Overarching practices applied to every area; ties workload-level controls to org-level governance.

- id: SEC01-BP01
  title: Separate workloads using accounts
  one_liner: Use a multi-account strategy to isolate environments and workloads at the account boundary.
  keywords: multi-account, AWS Organizations, OU, account separation, prod/dev/test isolation, landing zone, Control Tower

- id: SEC01-BP02
  title: Secure account root user and properties
  one_liner: Lock down the root user; remove access keys, enforce MFA, restrict use to root-only tasks.
  keywords: root user, root credentials, root MFA, account contacts, billing email, root access keys

- id: SEC01-BP03
  title: Identify and validate control objectives
  one_liner: Derive control objectives from compliance/threat requirements and validate they are met.
  keywords: control objectives, compliance mapping, audit, regulatory requirements, validate controls

- id: SEC01-BP04
  title: Stay up to date with security threats and recommendations
  one_liner: Track new threats, CVEs, and AWS security guidance; feed them into your controls.
  keywords: threat intel, CVE, security bulletins, AWS Security Bulletins, advisories, patch cadence

- id: SEC01-BP05
  title: Reduce security management scope
  one_liner: Use managed services and automation to shrink what your team must secure directly.
  keywords: managed services, reduce scope, shared responsibility, serverless security, abstraction

- id: SEC01-BP06
  title: Automate deployment of standard security controls
  one_liner: Deploy baseline guardrails (SCPs, Config rules, baselines) as code across all accounts.
  keywords: guardrails, SCP, AWS Config rules, baselines, infra as code, controls as code, CloudFormation, Terraform

- id: SEC01-BP07
  title: Identify threats and prioritize mitigations using a threat model
  one_liner: Run threat modeling (e.g. STRIDE) to find and prioritize threats before/while building.
  keywords: threat model, STRIDE, attack tree, threat-dragon, threat modeling workshop

- id: SEC01-BP08
  title: Evaluate and implement new security services and features regularly
  one_liner: Continuously evaluate new AWS security services/features and roll out those that help.
  keywords: new AWS services, feature evaluation, security roadmap, re:Invent security launches

### Domain: Identity and access management → Identity management

#### SEC02 — How do you manage identities for people and machines?
Human (workforce, third-party, end-user) and machine identities and how they sign in.

- id: SEC02-BP01
  title: Use strong sign-in mechanisms
  one_liner: Enforce MFA, strong password policies, and phishing-resistant auth for human sign-in.
  keywords: MFA, passkeys, WebAuthn, FIDO2, password policy, phishing-resistant, sign-in

- id: SEC02-BP02
  title: Use temporary credentials
  one_liner: Prefer short-lived credentials (STS, IAM roles, Identity Center) over long-lived keys.
  keywords: temporary credentials, STS, AssumeRole, IAM roles, IAM Identity Center, SSO, no static keys

- id: SEC02-BP03
  title: Store and use secrets securely
  one_liner: Store secrets in a managed secret store and retrieve at runtime; rotate automatically.
  keywords: Secrets Manager, Parameter Store, secret rotation, KMS-encrypted secrets, no secrets in code

- id: SEC02-BP04
  title: Rely on a centralized identity provider
  one_liner: Federate all human access through one IdP (Identity Center, Okta, Entra ID, etc.).
  keywords: SSO, federation, IdP, SAML, OIDC, IAM Identity Center, Okta, Entra ID, central identity

- id: SEC02-BP05
  title: Audit and rotate credentials periodically
  one_liner: Inventory credentials, detect unused/stale ones, rotate or revoke on a schedule.
  keywords: credential rotation, access key rotation, IAM credential report, unused credentials, key age

- id: SEC02-BP06
  title: Employ user groups and attributes
  one_liner: Manage access by groups and attributes (ABAC) instead of per-user policies.
  keywords: ABAC, RBAC, groups, attributes, tags-based access, attribute-based access control

### Domain: Identity and access management → Permissions management

#### SEC03 — How do you manage permissions for people and machines?
How you authorize identities to act on resources, including least privilege and cross-account sharing.

- id: SEC03-BP01
  title: Define access requirements
  one_liner: Document who/what needs access to which resources under which conditions.
  keywords: access requirements, access model, who needs access, role catalog, access design

- id: SEC03-BP02
  title: Grant least privilege access
  one_liner: Give identities only the permissions required for the task — no more.
  keywords: least privilege, fine-grained IAM, scope down, deny-by-default, minimum permissions, policy tightening

- id: SEC03-BP03
  title: Establish emergency access process
  one_liner: Define a break-glass procedure for emergency administrative access with auditing.
  keywords: break-glass, emergency access, incident access, fire-call, audited admin

- id: SEC03-BP04
  title: Reduce permissions continuously
  one_liner: Use access analyzers/usage data to remove unused permissions over time.
  keywords: IAM Access Analyzer, last accessed, unused permissions, permission right-sizing, policy reduction

- id: SEC03-BP05
  title: Define permission guardrails for your organization
  one_liner: Use SCPs/RCPs and other org-level controls to cap what any account or identity can do.
  keywords: SCP, RCP, Organizations, guardrail, deny boundary, permission boundary, org-wide deny

- id: SEC03-BP06
  title: Manage access based on lifecycle
  one_liner: Provision, change, and revoke access tied to joiner/mover/leaver and machine lifecycle.
  keywords: JML, joiner mover leaver, deprovisioning, lifecycle access, offboarding, role change

- id: SEC03-BP07
  title: Analyze public and cross-account access
  one_liner: Detect resources exposed publicly or to other accounts and validate intent.
  keywords: IAM Access Analyzer, public S3, public buckets, cross-account access, external access findings

- id: SEC03-BP08
  title: Share resources securely within your organization
  one_liner: Share resources between your own accounts using RAM/Organizations with proper scoping.
  keywords: AWS RAM, resource sharing, cross-account sharing, share within org, organization sharing

- id: SEC03-BP09
  title: Share resources securely with a third party
  one_liner: Share with external parties using external IDs, scoped roles, and confused-deputy protections.
  keywords: third-party access, external ID, cross-account role, vendor access, confused deputy

### Domain: Detection

#### SEC04 — How do you detect and investigate security events?
Logging, telemetry, anomaly detection, and remediation triggers.

- id: SEC04-BP01
  title: Configure service and application logging
  one_liner: Enable logging on all relevant AWS services and your applications.
  keywords: CloudTrail, VPC Flow Logs, S3 access logs, ELB logs, application logging, audit logs

- id: SEC04-BP02
  title: Capture logs, findings, and metrics in standardized locations
  one_liner: Centralize logs and security findings into a single, queryable destination.
  keywords: log aggregation, log centralization, Security Lake, Security Hub, SIEM, central logging account

- id: SEC04-BP03
  title: Correlate and enrich security alerts
  one_liner: Correlate findings across sources and enrich with context for faster triage.
  keywords: correlate alerts, SIEM, Security Hub, GuardDuty, enrichment, detection rules

- id: SEC04-BP04
  title: Initiate remediation for non-compliant resources
  one_liner: Automatically remediate findings (e.g. via SSM/Lambda/Config) when safe to do so.
  keywords: auto remediation, Config remediation, SSM automation, Security Hub auto-response, EventBridge

### Domain: Infrastructure protection → Networks

#### SEC05 — How do you protect your network resources?
Network design, segmentation, traffic inspection.

- id: SEC05-BP01
  title: Create network layers
  one_liner: Design layered VPCs/subnets to isolate trust zones (public, private, data).
  keywords: VPC design, subnet tiers, public/private subnets, network segmentation, layered network

- id: SEC05-BP02
  title: Control traffic flow within your network layers
  one_liner: Use security groups, NACLs, route tables to enforce intended east-west and north-south flow.
  keywords: security groups, NACL, route tables, traffic control, east-west, north-south, microsegmentation

- id: SEC05-BP03
  title: Implement inspection-based protection
  one_liner: Inspect traffic with WAF, Network Firewall, GWLB-attached IDS/IPS appliances.
  keywords: AWS WAF, Network Firewall, IDS, IPS, GWLB, traffic inspection, DPI

- id: SEC05-BP04
  title: Automate network protection
  one_liner: Provision and update network controls automatically as workloads change.
  keywords: automate firewall rules, IaC network, dynamic security groups, Firewall Manager

### Domain: Infrastructure protection → Compute

#### SEC06 — How do you protect your compute resources?
Hardening, patching, and access for EC2, containers, Lambda, etc.

- id: SEC06-BP01
  title: Perform vulnerability management
  one_liner: Scan compute (OS, images, containers, Lambda) for vulnerabilities and remediate.
  keywords: vulnerability scanning, Amazon Inspector, image scanning, ECR scan, patching, CVE remediation

- id: SEC06-BP02
  title: Provision compute from hardened images
  one_liner: Build/use hardened, attested base images (golden AMIs/container images).
  keywords: hardened image, golden AMI, EC2 Image Builder, base image, CIS-hardened, attested images

- id: SEC06-BP03
  title: Reduce manual management and interactive access
  one_liner: Replace SSH/RDP with automated workflows; use SSM Session Manager when access is needed.
  keywords: no SSH, no RDP, SSM Session Manager, immutable infra, automation over interactive

- id: SEC06-BP04
  title: Validate software integrity
  one_liner: Verify signatures/digests on artifacts and images before running them.
  keywords: signed artifacts, code signing, image signing, cosign, Notation, supply chain integrity, SBOM verify

- id: SEC06-BP05
  title: Automate compute protection
  one_liner: Automate hardening, patching, scanning, and response for compute resources.
  keywords: Patch Manager, automated patching, auto-hardening, Inspector automation, Lambda runtime updates

### Domain: Data protection → Classification

#### SEC07 — How do you classify your data?
Understand sensitivity to apply right protection.

- id: SEC07-BP01
  title: Understand your data classification scheme
  one_liner: Define and document sensitivity tiers (public, internal, confidential, restricted).
  keywords: data classification, sensitivity tiers, PII, PHI, data taxonomy

- id: SEC07-BP02
  title: Apply data protection controls based on data sensitivity
  one_liner: Apply encryption, access controls, retention based on each tier.
  keywords: control by sensitivity, tier-based controls, PII handling, restricted data controls

- id: SEC07-BP03
  title: Automate identification and classification
  one_liner: Use tools like Macie to automatically discover and label sensitive data.
  keywords: Amazon Macie, auto-classification, data discovery, sensitive data scanning, DLP

- id: SEC07-BP04
  title: Define scalable data lifecycle management
  one_liner: Manage retention, archival, and deletion across the data lifecycle.
  keywords: data lifecycle, S3 Lifecycle, retention policy, archival, deletion, data minimization

### Domain: Data protection → At rest

#### SEC08 — How do you protect your data at rest?
Encryption, key management, access control on stored data.

- id: SEC08-BP01
  title: Implement secure key management
  one_liner: Manage encryption keys with KMS/CloudHSM, with rotation, scoped policies, and auditing.
  keywords: KMS, CloudHSM, key rotation, key policy, key access, envelope encryption, customer-managed key

- id: SEC08-BP02
  title: Enforce encryption at rest
  one_liner: Require encryption on all persistent storage (S3, EBS, RDS, DynamoDB, EFS, etc.).
  keywords: encryption at rest, S3 default encryption, EBS encryption, RDS encryption, DynamoDB encryption

- id: SEC08-BP03
  title: Automate data at rest protection
  one_liner: Auto-detect and remediate unencrypted storage; enforce via Config/SCPs.
  keywords: enforce encryption, Config rule encryption, SCP deny unencrypted, auto remediation encryption

- id: SEC08-BP04
  title: Enforce access control
  one_liner: Apply strict, auditable access controls on stored data (bucket policies, RDS auth, etc.).
  keywords: bucket policy, S3 Block Public Access, IAM data access, resource policy, data access control

### Domain: Data protection → In transit

#### SEC09 — How do you protect your data in transit?
Encryption and authentication for data on the wire.

- id: SEC09-BP01
  title: Implement secure key and certificate management
  one_liner: Manage TLS certs and signing keys centrally with rotation (ACM, ACM PCA).
  keywords: ACM, ACM PCA, TLS certs, certificate rotation, private CA, cert management

- id: SEC09-BP02
  title: Enforce encryption in transit
  one_liner: Require TLS for all in-transit traffic; disallow plaintext protocols.
  keywords: TLS, HTTPS, encryption in transit, no plaintext, TLS 1.2/1.3, redirect HTTP to HTTPS

- id: SEC09-BP03
  title: Authenticate network communications
  one_liner: Authenticate peer endpoints (mTLS, SigV4, VPC endpoint policies) — not just encrypt.
  keywords: mTLS, mutual TLS, SigV4, VPC endpoint policy, authenticated channels, service identity

### Domain: Incident response

#### SEC10 — How do you anticipate, respond to, and recover from incidents?
Preparation, operations during an incident, and post-incident learning.

- id: SEC10-BP01
  title: Identify key personnel and external resources
  one_liner: Maintain an up-to-date list of internal responders and external contacts (AWS Support, IR firms).
  keywords: IR contacts, on-call list, AWS Support tier, external IR retainer, stakeholders

- id: SEC10-BP02
  title: Develop incident management plans
  one_liner: Document IR plans covering severity levels, comms, roles, escalation.
  keywords: incident response plan, IR plan, severity levels, comms plan, escalation

- id: SEC10-BP03
  title: Prepare forensic capabilities
  one_liner: Pre-build forensic accounts, tooling, and procedures to preserve evidence.
  keywords: forensics, evidence preservation, forensic account, EBS snapshot capture, memory dump

- id: SEC10-BP04
  title: Develop and test security incident response playbooks
  one_liner: Maintain runbooks per incident type and exercise them regularly.
  keywords: runbook, playbook, IR procedures, incident-specific playbook

- id: SEC10-BP05
  title: Pre-provision access
  one_liner: Pre-stage IR roles and access so responders aren't blocked during an incident.
  keywords: pre-provisioned IR role, break-glass IR access, responder permissions

- id: SEC10-BP06
  title: Pre-deploy tools
  one_liner: Have IR tooling already deployed and tested before an incident occurs.
  keywords: pre-deployed IR tooling, EventBridge IR, Lambda IR automation, forensic AMIs

- id: SEC10-BP07
  title: Run simulations
  one_liner: Run game days/tabletop exercises to test plans and tooling.
  keywords: game day, tabletop exercise, IR simulation, red/purple team exercise

### Domain: Application security

#### SEC11 — How do you incorporate and validate the security properties of applications throughout the design, development, and deployment lifecycle?

- id: SEC11-BP01
  title: Train for application security
  one_liner: Build appsec skills across builders; provide ongoing secure-coding training.
  keywords: appsec training, secure coding training, developer security, OWASP training

- id: SEC11-BP02
  title: Automate testing throughout the development and release lifecycle
  one_liner: Add SAST, DAST, SCA, IaC scanning, secrets scanning into the CI/CD pipeline.
  keywords: SAST, DAST, SCA, secrets scanning, IaC scanning, CI security tests, shift-left

- id: SEC11-BP03
  title: Perform regular penetration testing
  one_liner: Pen-test apps and infrastructure on a defined cadence and after major changes.
  keywords: pen test, penetration testing, red team, external pentest, app pentest

- id: SEC11-BP04
  title: Conduct code reviews
  one_liner: Require code review focused on security for changes touching sensitive areas.
  keywords: code review, security review, PR review, secure code review

- id: SEC11-BP05
  title: Centralize services for packages and dependencies
  one_liner: Use a curated internal registry/proxy for OSS packages; control what enters builds.
  keywords: CodeArtifact, private registry, dependency proxy, allowlisted packages, package mirror

- id: SEC11-BP06
  title: Deploy software programmatically
  one_liner: Use automated, repeatable deployments — no manual production changes.
  keywords: CI/CD, automated deploy, no manual deploy, repeatable releases, GitOps

- id: SEC11-BP07
  title: Regularly assess security properties of the pipelines
  one_liner: Audit the build/release pipeline itself: access, signing, integrity, isolation.
  keywords: pipeline security, build infra hardening, supply chain, SLSA, runner security, secrets in CI

- id: SEC11-BP08
  title: Build a program that embeds security ownership in workload teams
  one_liner: Security champions / embedded ownership so each workload team owns its security.
  keywords: security champions, embedded security, security ownership, dev-led security

---

## Matcher instructions (for the subagent)

When asked to map a topic:

1. Read the user's topic statement.
2. **Decide `mode`** first:
   - `mode: domain` — phrasing signals a whole-domain discussion. Triggers include:
     - "whole/entire/all of <domain>"
     - "review <domain>", "<domain> in general"
     - "everything about <domain>"
     - "let's look at <domain> as a whole"
   - `mode: control` — anything more specific. Default.
3. For `mode: control`: score every BP by keyword overlap and semantic fit. Pick the **single best** `SECxx-BPyy`. Always prefer a specific BP over just naming a domain.
4. For `mode: domain`: identify the single best-matching domain. Leave `sec_question`, `control_id`, `control_title` blank.
5. Set `confidence` (for the BP/domain pick):
   - `high` — at least one explicit keyword match AND the topic clearly belongs to that BP/domain.
   - `medium` — semantic match but no keyword hit, or topic could split between 2 BPs.
   - `low` — topic only loosely relates; no good BP fits (in this case still return the closest pillar/domain).
6. Return up to 2 `alternatives` (other plausible BP ids; empty in domain mode).
7. **Always emit `domain_slug`** using the canonical slug from the Domains table above.
8. **Detect `scope`** independently from the Scopes table:
   - Scan the topic for cues from the "Scope detection cues" table (case-insensitive substring match).
   - If exactly one scope's cues fire → that scope.
   - If cues from ≥2 different scopes fire, OR phrasing like "everywhere/all scopes/across code and cloud" → `cross`.
   - If zero cues fire → `unknown`. Do not guess.
   - Set `scope_confidence`: `high` if ≥2 distinct cues for the picked scope, `medium` if exactly 1 cue, `low`/`unknown` otherwise.
   - List the actual cues that fired in `scope_cues` (comma-separated; empty if `scope: unknown`).
9. Keep `reasoning` to ≤2 sentences and cover both BP and scope picks.

Output shape (plain text, easy to parse):

```
mode: control | domain
pillar: Security
domain: <domain name>
domain_slug: <slug>
sec_question: SECxx                       # empty if mode=domain
control_id: SECxx-BPyy                    # empty if mode=domain
control_title: <title>                    # empty if mode=domain
scope: code | cloud | cluster | container | cross | unknown
scope_confidence: high | medium | low
scope_cues: <comma-separated cues>        # empty if scope=unknown
confidence: high|medium|low
alternatives: SECaa-BPbb, SECcc-BPdd
reasoning: <≤2 sentences>
```
