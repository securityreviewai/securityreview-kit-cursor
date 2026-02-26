---
name: PWNISMS Threat Modelling
description: Lightweight, security-first threat modelling workflow for code and architecture tasks. Use it to walk all 7 PWNISMS categories, identify plausible threats, prioritize top risks, and propose concrete mitigations before and during implementation.
---

# PWNISMS — On-the-Go Threat Modelling

For EVERY security-relevant task (feature, bug fix, refactor, infra change, architecture design), run a lightweight threat model with PWNISMS.

- Walk through all 7 categories explicitly.
- If a category is not applicable, state it briefly and move on.
- Anchor analysis to linked files, diffs, PRs, API specs, and diagrams whenever available.
- Focus on realistic threats for the current context, not exhaustive attack catalogs.

---

## Inputs to Gather First

Collect these quickly before deep analysis:

- **Scope**: What is changing (feature, component, service, migration, PR)?
- **Assets**: What must be protected (PII, credentials, tokens, configs, accounts, workflows)?
- **Entry points**: How data enters/leaves (HTTP, queues, schedulers, CLI, webhooks, integrations)?
- **Trust boundaries**: Where data crosses users/services/networks/privilege levels?

If the user provided specific code, diffs, or architecture artifacts, prioritize those as primary evidence.

---

## Workflow (PWNISMS)

1. **Clarify scope and assumptions**
   - Define the exact unit of analysis.
   - State assumptions explicitly (auth model, deployment boundary, tenant model, etc.).
2. **Map assets and flows**
   - List high-value assets and critical data paths.
   - List entry points and exits across trust boundaries.
3. **Walk all 7 PWNISMS categories**
   - Identify plausible threats for each category.
   - Keep findings concrete and contextual.
4. **Prioritize**
   - Select the top 3-7 risks by impact and likelihood.
5. **Mitigate**
   - Propose concrete, implementable controls for each prioritized risk.
6. **Summarize residual risk**
   - Call out remaining risk, trade-offs, and follow-up actions.
   - Call out unknowns instead of silently guessing.

---

## The 7 Categories (What to Check)

### P — Product

Application and business-logic threats:

- Input validation, injection, insecure deserialization.
- Authorization gaps, privilege escalation, IDOR/BOLA.
- Business logic abuse, replay/race conditions, unsafe redirects.
- Error handling that leaks internals.

### W — Workload

Compute and infrastructure threats:

- Insecure container/runtime posture, over-privileged workload identity.
- Weak host/orchestrator controls and segmentation.
- Insecure data storage/backups and DB configuration.
- Queue/broker abuse and poison-message handling gaps.

### N — Network

Network and transport threats:

- Missing/weak TLS, insecure service-to-service communication.
- Exposed ports/endpoints and permissive ingress/egress.
- Weak segmentation or lateral movement paths.
- API-layer abuse controls missing (rate limits, request limits, CORS hardening).

### I — IAM (Identity & Access Management)

Identity and authorization threats:

- Broken authentication controls and token validation.
- Missing least-privilege RBAC/ABAC.
- Service-to-service auth gaps.
- Escalation paths across users, roles, or services.

### S — Secrets

Credential and key management threats:

- Secrets in code, images, logs, CI output, or defaults.
- Weak rotation, revocation, or token lifetime policies.
- Over-shared secrets across components.
- Missing secret manager/KMS controls.

### M — Monitoring (Logging & Observability)

Detection and auditability threats:

- Missing logs for auth, authorization, admin/data access events.
- Sensitive data leakage in logs.
- Missing alerts for abuse indicators.
- Incomplete audit trails or weak log integrity.

### S — Supply Chain

Dependency and delivery threats:

- Unpinned/unverified dependencies and vulnerable packages.
- Third-party integration trust and scope overreach.
- CI/CD pipeline leakage or unreviewed build scripts.
- Unsigned/unprovenanced artifacts, missing SBOM.
- Treat AI-generated code as untrusted until validated.

---

## Tailor for Architecture / Design Tasks

When discussing designs before code exists:

- Sketch a mental data flow: actors, data sent/received, storage, processing points.
- Mark trust boundaries explicitly (client-backend, backend-DB, service-service, cloud-third party).
- Identify where strong authentication/authorization is mandatory.
- Identify where encryption in transit and at rest is mandatory.
- Recommend concrete security patterns:
  - Parameterized queries / ORM for DB access.
  - Centralized authn/authz and role checks.
  - Secrets manager / KMS for credentials and keys.
  - mTLS or signed requests for service-to-service calls.

---

## Security-First Code Generation Rules

When implementing code, enforce these baseline controls:

1. Validate and constrain all untrusted input.
2. Parameterize all queries and command-like invocations.
3. Enforce least privilege for users, services, and workloads.
4. Never hardcode secrets; use managed secret stores.
5. Encrypt sensitive data in transit and at rest.
6. Log security-relevant actions without leaking secrets/PII.
7. Pin and verify dependencies and build artifacts.
8. Return safe user errors; keep sensitive diagnostics internal.
9. Add abuse protections (rate limits, lockouts, throttling) on exposed interfaces.

---

## Post-Generation Checklist

Before finalizing output, confirm:

- [ ] Scope, assumptions, and trust boundaries were explicit.
- [ ] All 7 PWNISMS categories were checked (or marked N/A explicitly).
- [ ] Top risks were prioritized by impact and likelihood.
- [ ] Mitigations are concrete and actionable.
- [ ] Residual risk and follow-up actions are stated.

If ANY box cannot be checked, you MUST flag the gap to the user with a specific remediation recommendation before finalizing the code.
