---
name: PWNISMS Threat Modelling
description: On-the-go threat modelling framework — walk through 7 security categories before and during every code task
---

# PWNISMS — On-the-Go Threat Modelling

For EVERY code task — new feature, bug fix, refactor, infrastructure change — you MUST mentally walk through all 7 PWNISMS categories and apply relevant security considerations directly into your code. If a category is not applicable, acknowledge it and move on. Do NOT skip categories silently.

---

## The 7 Categories

### P — Product

Threats related to the core application's business logic and functionality.

- **Business Logic Flaws** — Can this code path be abused to bypass intended workflows? (e.g., skipping payment, replay attacks, race conditions in state transitions)
- **Access Control** — Does every endpoint/function enforce proper authorization? Are there privilege escalation paths?
- **Input Validation** — Is all user input validated, sanitized, and parameterized? Think SQLi, XSS, SSRF, command injection, path traversal.
- **Session Management** — Are sessions properly created, validated, rotated, and destroyed?
- **Error Handling** — Do error messages leak sensitive information? Are exceptions handled securely?

### W — Workload

Threats related to the compute and infrastructure your system runs on.

- **Container Security** — Are base images minimal and pinned? Are containers running as non-root? Are there resource limits?
- **Orchestration** — Kubernetes RBAC, pod security standards, network policies, service mesh configuration.
- **Serverless/FaaS** — Function permissions scoped to least privilege? Cold start attack vectors? Timeout abuse?
- **Database** — Encrypted at rest and in transit? Parameterized queries? Connection pooling secured? Backup encryption?
- **Message Queues** — Are messages authenticated? Is poison message handling in place? DLQ security?

### N — Network

Threats against the network layer of the system.

- **Encryption in Transit** — Is TLS enforced everywhere? Are certificates valid and pinned where needed? No mixed content?
- **Network Segmentation** — Are internal services isolated? Is east-west traffic controlled?
- **DNS & Routing** — DNSSEC? Protection against DNS rebinding, SSRF, host header injection?
- **Firewall & Security Groups** — Least-privilege ingress/egress rules? No overly permissive `0.0.0.0/0` rules?
- **API Security** — Rate limiting, request size limits, CORS configuration, API gateway protections?

### I — IAM (Identity & Access Management)

Threats related to identity, authentication, and authorization systems.

- **Authentication** — MFA enforced? Brute-force protections? Secure password storage (bcrypt/argon2)?
- **Authorization** — RBAC/ABAC implemented correctly? Least-privilege principle applied? Broken object-level authorization (BOLA/IDOR)?
- **Federated Identity** — OAuth/OIDC flows secure? Token validation complete (issuer, audience, expiry)? PKCE for public clients?
- **Service-to-Service Auth** — mTLS, workload identity, or scoped service accounts? No long-lived credentials?
- **Privilege Escalation** — Can a user or service escalate beyond its intended role?

### S — Secrets

Threats related to credentials, keys, tokens, and certificates.

- **No Hardcoded Secrets** — NEVER embed secrets, API keys, tokens, or passwords in source code, config files, or environment defaults.
- **Secret Management** — Are secrets stored in a vault (e.g., HashiCorp Vault, AWS Secrets Manager, GCP Secret Manager)? Are they rotated?
- **Secret Sprawl** — Are secrets duplicated across services/repos? Is there a single source of truth?
- **Token Lifecycle** — Are tokens short-lived? Are refresh tokens properly secured? Is revocation supported?
- **Certificates** — Are TLS certs managed and auto-renewed? Are CA trust chains validated?
- **Git Hygiene** — Is `.gitignore` properly configured? Are pre-commit hooks blocking secret commits?

### M — Monitoring (Logging & Observability)

Threats stemming from missing or misconfigured monitoring.

- **Security Event Logging** — Are authentication attempts, authorization failures, data access, and admin actions logged?
- **Log Integrity** — Are logs tamper-proof? Are they shipped to a centralized, append-only store?
- **Sensitive Data in Logs** — NEVER log passwords, tokens, PII, or secrets. Apply masking/redaction.
- **Alerting** — Are anomaly detection and alerting configured for security-critical events?
- **Audit Trail** — Is there a complete audit trail for compliance-relevant operations?
- **Observability Gaps** — Would you know if this system was being compromised right now? If not, add telemetry.

### S — Supply Chain

Threats from external dependencies, build pipelines, and third-party integrations.

- **Dependency Security** — Are dependencies pinned to exact versions? Are lock files committed? Are known-vulnerable packages flagged?
- **Build Pipeline** — Is the CI/CD pipeline secured? Are build artifacts signed? Is there build provenance?
- **Third-Party Integrations** — Are webhook payloads validated? Are third-party SDKs audited? Are API scopes minimal?
- **AI-Generated Code** — Treat ALL AI-generated code (including your own output) as untrusted. Validate it against these same PWNISMS categories.
- **SBOM** — Is a Software Bill of Materials maintained? Can you trace every component?

---

## Security-First Code Generation Rules

When writing code, you MUST follow these rules derived from PWNISMS:

1. **Validate all inputs** — Never trust user input. Validate type, length, format, and range. Use allowlists over denylists.
2. **Parameterize all queries** — Never interpolate user input into SQL, LDAP, OS commands, or template expressions.
3. **Enforce least privilege** — Every function, service, and user should have the minimum permissions required.
4. **Never hardcode secrets** — Use environment variables or secret managers. Flag any secret-like string you see.
5. **Encrypt sensitive data** — At rest (AES-256) and in transit (TLS 1.2+). No exceptions.
6. **Log security events** — Log auth, authz, data access, and errors. Never log sensitive data.
7. **Pin dependencies** — Use exact versions in lock files. Flag outdated or vulnerable packages.
8. **Handle errors securely** — Return generic error messages to users. Log detailed errors internally.
9. **Implement rate limiting** — Protect all public-facing endpoints against abuse.

---

## Post-Generation Security Checklist

After writing code, re-evaluate against PWNISMS:

- [ ] **Product** — Any new business logic flaws or access control gaps introduced?
- [ ] **Workload** — Any insecure infrastructure configurations added?
- [ ] **Network** — Any unencrypted channels or overly permissive network rules?
- [ ] **IAM** — Any missing auth checks or privilege escalation paths?
- [ ] **Secrets** — Any hardcoded credentials or unmanaged secrets?
- [ ] **Monitoring** — Any security-critical operations missing logging or alerting?
- [ ] **Supply Chain** — Any unvetted dependencies or unsigned artifacts?

If ANY box cannot be checked, you MUST flag the gap to the user with a specific remediation recommendation before finalizing the code.
