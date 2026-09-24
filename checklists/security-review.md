# Security Review Checklist

Review of a change or system for exploitable weaknesses and policy violations. Run on security-adjacent changes at change time and on standing systems periodically. Runs alongside checklists/change-review.md; findings flow back as blockers.

**Sources:** transitive — items derive from `references/security.md`; resolve the chain through `references/source-map.md`. No primary source is cited directly — do not attribute a checklist item to OWASP or NIST.

## Applicability

Mandatory when the change touches: credentials/secrets, authentication or authorization, network access, subprocess/shell execution, filesystem, sandboxing/isolation, untrusted input handling, or sensitive data. Tier by exposure — the skill's T1–T4 scale:

| Tier | Surface class | Depth |
|---|---|---|
| T1 | Security-adjacent diff (one surface, no new trust boundary) | Targeted: trust boundary, input validation, secrets, injection for the touched surface |
| T2 | New capability or trust boundary | Full checklist + threat-model update |
| T3 | Authentication, credential handling, injection surface, public exposure | Full + adversarial pass (threat-model exercise, hostile-input fuzzing) |
| T4 | Compliance scope, critical data, large user base | Full + external review and formal residual-risk sign-off |

## Required evidence

- The diff and a one-page map of trust boundaries and data flow for the affected surface (or the delta to the existing threat model), per references/security.md.
- Permissions and policy configuration as deployed, not as intended.
- Secrets inventory for the surface: where credentials are stored, who can read them, how they rotate.
- Test evidence: denial-path tests, hostile-input and boundary tests, and the runs that executed them.
- SAST/SCA output for the change; CVE history of new dependencies; SBOM entries where one is maintained.
- For T3/T4: the threat model and the adversarial test log.

## Decision / blocker criteria

- **Block:** credential exposure or missing authorization on a privileged operation; injection through a real boundary (command, prompt, SQL, path); unsafe deserialization; secrets in logs or telemetry; insecure default configuration; exploitable known-vulnerable dependency on the exposed path without an approved exception; isolation or sandbox escape.
- **Defer (track with owner and deadline):** defense-in-depth hardening beyond the exposed surface, rare-path denial-of-service tuning.
- **Pass:** no exploitable finding in scope; residual risks recorded with owner, deadline, and the accepted impact.
- **Reporting:** one finding per line — surface, attack, impact, evidence (repro or test), severity.

## N/A behavior

`N/A` means the condition genuinely does not apply to this surface, with a one-line reason (e.g., "no outbound network in this component", "filesystem access is read-only and isolated"). A surface-level `N/A` must be stated for the whole surface, not applied item by item for convenience. Unknown or unverified is a gap, not `N/A`.

## Checks

- [ ] **SR-01 Threat model current.** Assets, trust boundaries, entry points, and actors are mapped for the surface; the model is updated on any architectural or major dependency change, per references/security.md.
- [ ] **SR-02 Validation at trust boundaries.** Input is validated at parser/config, wire, storage, and process boundaries — not redundantly re-validated or skipped in typed same-process code.
- [ ] **SR-03 Injection through execution.** Shell commands, subprocess arguments, queries, and paths are built without untrusted interpolation; metacharacters cannot escape.
- [ ] **SR-04 Path traversal.** File operations confine paths under the intended root; `..` and symlink escape are rejected.
- [ ] **SR-05 File permissions.** Written files and directories use least-privilege modes; no world-writable secrets or caches.
- [ ] **SR-06 Secrets.** No secrets in source, configuration, or logs; credentials live in a vault/KMS and are injected at runtime; rotation and revocation are documented, per references/security.md.
- [ ] **SR-07 Transport security.** Where the system communicates over untrusted networks, external traffic uses enforced TLS — minimum TLS 1.2, prefer TLS 1.3 where supported — with weak protocol/cipher configurations disabled; no plaintext fallback; certificate validation is on.
- [ ] **SR-08 Server-side request forgery.** Outbound fetch resolves and reaches only intended targets; redirects and DNS rebinding are handled.
- [ ] **SR-09 Deserialization.** No unsafe deserialization from untrusted sources; formats are strict and bounded.
- [ ] **SR-10 Isolation coverage.** Privileged operations run under declared isolation (container, VM, seccomp, sandbox); the review names what escapes it.
- [ ] **SR-11 Authorization and secure defaults.** Access control is enforced server-side at every API and function, centralized in one auditable policy module, and denied by default; client-supplied roles are never trusted; a fresh install or configuration starts locked down, with insecure options requiring explicit opt-in — per references/security.md.
- [ ] **SR-12 Untrusted content in execution paths.** Content that influences commands, prompts, or queries is treated as untrusted; injection mitigations apply where it matters.
- [ ] **SR-13 Disclosure-safe failures and audit.** Error messages and telemetry reveal no internals, stack traces, or secrets to untrusted parties; security-relevant events (failed logins, denied access, admin actions, secret access) are logged without secrets or personal data, per references/security.md.
- [ ] **SR-14 Dependency audit.** SCA runs in CI with fix SLAs by severity; findings block on exploitability and exposure, with an approved-exception path, not on the severity label alone — per references/security.md.
- [ ] **SR-15 Rate limiting.** Abuse-prone surfaces (authentication, web fetch, external calls) have configurable limits, per checklists/production-readiness.md (PR-13).
- [ ] **SR-16 Data protection.** Sensitive data is classified and controls applied by class; encryption at rest uses managed keys where warranted; retention and purge are defined, per references/security.md.
- [ ] **SR-17 Supply chain.** Where supply-chain risk or compliance warrants it, builds are hermetic and reproducible with signed provenance, and artifacts are verified at deployment; no secrets in build logs — per references/security.md.
- [ ] **SR-18 Residual risk ledger.** Every accepted risk has owner, deadline, and stated impact; the ledger is reviewed at T3/T4.
- [ ] **SR-19 Authorization migration — defaults and compatibility.** Where the authorization model, tokens, or claims change, secure defaults and backward compatibility are resolved explicitly, with staged/audit-only or shadow evaluation where safe; contract tests cover existing clients; token/claim compatibility is verified before cutover.
- [ ] **SR-20 Authorization migration — observability and rollback.** Deny-reason telemetry exists without leaking sensitive details, and rollback does not silently reopen access — the post-rollback state is verified to match the pre-migration state.
