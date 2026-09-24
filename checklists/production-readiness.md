# Production Readiness Checklist

Go-live gate for shipping a change, feature, or service to production users — and the re-entry gate after an incident (see checklists/incident-review.md). Run by the release owner with the author, reviewed by an independent approver.

**Sources:** transitive — items derive from `references/delivery.md`, `references/observability-sre.md`, `references/security.md`, and `references/incident-chaos.md`; resolve the chain through `references/source-map.md`. No primary source is cited directly — do not attribute a checklist item to OWASP, NIST, or a book.

## Applicability

Run before any of: first production deployment, feature-flag flip to real users, new external dependency, new data store or schema migration, post-incident re-entry. Tier by blast radius and reversibility — the skill's T1–T4 scale:

| Tier | Deployment class | Depth |
|---|---|---|
| T1 | Internal/demo deployment, staging promotion | Core gate: artifacts, config, smoke, rollback |
| T2 | Production rollout of an existing surface | Full checklist |
| T3 | New user-facing service, schema/data migration, high blast radius | Full + load test and restore rehearsal |
| T4 | Compliance-scoped or data-loss-critical rollout | Full + rehearsed DR, formal sign-off |

## Required evidence

Attach artifacts, not assurances:

- Build artifacts with provenance for T3/T4 or where supply-chain risk exists: CI logs, image digests/SBOM hashes of the exact thing being deployed.
- Rollout plan: staged percentage, rollback procedure, and the person who executes it; feature-flag configuration where flags are used.
- CI results for the shipped commit: tests, typecheck, lint, hygiene, snapshots; end-to-end checks on the target environment.
- Load/soak results at or above expected peak for T3/T4 (or the documented reason they are not needed).
- Runbook: restart, rollback, debug, and escalation steps for this service.
- Monitoring and alert rules with thresholds, plus the dashboard that shows this change's health.
- Backup/restore verification record where the change touches durable data.
- Dependency and license scan output for new dependencies; SBOM entries where one is maintained.

## Decision / blocker criteria

- **Block:** no verified rollback path; data-loss or corruption risk without mitigation; secrets in configuration, logs, or artifacts; failing smoke on the target environment; new dependency without scan and license review; missing runbook for a T3/T4 surface; unverified restore where durable data is touched; SLO/error-budget impact unquantified where an SLO exists.
- **Defer:** performance tuning beyond the shipped slice, cosmetic issues, features outside the release scope.
- **Pass:** all applicable checks green; launch decision recorded with owner, date, and the exact artifact hash deployed.

## N/A behavior

`N/A` means the condition genuinely does not apply to this deployment, with a one-line reason (e.g., "no durable data written", "internal deployment, no SLO"). Unknown or unverified is a gap, not `N/A` — never a substitute for "not measured".

## Checks

- [ ] **PR-01 Artifact provenance.** Where supply-chain risk or compliance warrants it, the exact deployed build matches the reviewed commit and a hash/digest is recorded in the release record.
- [ ] **PR-02 Rollback path.** A concrete rollback procedure exists, rehearsed for T3/T4, and executes in bounded time.
- [ ] **PR-03 Feature flags.** Where risky or uncertain work ships behind flags, flags are deployed often, flipped when ready, and pruned on a schedule — per references/delivery.md.
- [ ] **PR-04 Migration plan.** Schema/data migrations are versioned, reversible or with a verified forward path, and tested against production-shaped data; no big-bang migrations.
- [ ] **PR-05 Backup/restore verified.** Restore was actually exercised from a recent backup, not just configured.
- [ ] **PR-06 Monitoring coverage.** The change's health signals (errors, latency, saturation) appear on a dashboard with alert thresholds, not only in logs, per references/observability-sre.md.
- [ ] **PR-07 Log hygiene.** No secrets, tokens, or PII in logs or telemetry at the configured level; log levels are deployable configuration, not code edits.
- [ ] **PR-08 SLO/error budget.** Where SLOs exist, the change's impact on them is quantified; new surfaces with an SLO get burn-aware alerting — per references/incident-chaos.md.
- [ ] **PR-09 Dependency review.** New dependencies are scanned for known CVEs, license-checked, and pinned; findings block on exploitability and exposure with an approved-exception path, not on severity label alone — per references/security.md. Record SBOM entries where one is maintained.
- [ ] **PR-10 Resource limits.** Memory, CPU, disk, and timeouts are bounded; misconfiguration fails loud at startup rather than silently degrading.
- [ ] **PR-11 Timeouts, retries, backoff.** Outbound calls have explicit time limits, bounded retries with backoff and jitter, and circuit behavior; no infinite waits.
- [ ] **PR-12 Idempotency.** Repeated submission or replay of the same request/event does not duplicate effects or corrupt state, per the skill's retry/replay rules.
- [ ] **PR-13 Rate limits and quotas.** Where the surface can be abused (API, web fetch, subprocess, external calls), limits exist and are configurable.
- [ ] **PR-14 Load validated.** For T3/T4, or where the deployment faces meaningful load, load/soak at or above expected peak passes; degradation is graceful (queuing, load shedding) rather than crashing.
- [ ] **PR-15 Transport security.** Where the system communicates over untrusted networks, external traffic uses enforced TLS — minimum TLS 1.2, prefer TLS 1.3 where supported — with weak protocol/cipher configurations disabled; no downgrade or plaintext fallback.
- [ ] **PR-16 Secrets management.** Production secrets come from a secret store or vault where one is available, injected at runtime — never source or configuration; rotation is documented, per references/security.md.
- [ ] **PR-17 Runbook exists.** Restart, rollback, debug, and escalation steps are written and current for the shipped surface.
- [ ] **PR-18 Owner and on-call coverage.** An owner is named for the rollout window; where the service runs unattended or 24-7, on-call coverage and verified escalation contacts apply.
- [ ] **PR-19 Support and docs.** User-facing behavior changes update the README/docs; the support channel knows what shipped.
- [ ] **PR-20 Third-party capacity.** Where external services are involved, they have capacity for expected traffic; quota and rate-limit implications are checked.
- [ ] **PR-21 Security sign-off.** checklists/security-review.md ran on the security-relevant surface and its findings are closed or tracked with owners.
