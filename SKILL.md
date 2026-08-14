---
name: engineering-excellence
description: "Use when writing, reviewing, designing, refactoring, debugging, or planning production-grade software — code, architecture, data systems, delivery, security, performance, infrastructure, incidents, or compliance. Also triggers on requests for engineering best practices, clean code, TDD/SDD/DDD/DADD, code reviews, production readiness, or post-incident reviews."
---

# Engineering Excellence

Production-grade engineering discipline distilled from 20+ books, standards, and authoritative sources — complexity reduction, clean architecture, clean code, construction discipline, data-intensive systems, DDD, enterprise patterns, refactoring, production hardening, pragmatism, legacy code, continuous delivery, observability/SRE, security engineering, performance, infrastructure as code, team process, incident response, and compliance. This SKILL.md routes each task to the few references and checklists that apply — it does not dump general advice.

## How to use this skill

1. **Classify** the task: risk tier (§1), paradigm (§2), areas touched (§5).
2. **Load** only the references and checklists the classification selects.
3. **Execute** the workflow (§4) under the selected paradigm.
4. **Finish** only when the evidence and completion rules (§6) are met.

> **Do not load every reference.** Load only what the classification selects — 1–3 references by default. Load more only when the scope genuinely requires it (for example, a migration spanning data, delivery, and observability); say why. Unused references stay unread.

## 1. Risk tiers

Classify by **impact** — blast radius, reversibility, who is affected, and recovery effort — not by file type. A one-line change to auth code can be T4; a 2,000-line feature behind a feature flag can be T2. The tier drives which checklists are mandatory.

- **T1 — Minimal impact**: failure is locally contained, immediately visible, and trivially reversible; no users, contracts, data, or security surfaces are affected. No checklist (T1 never runs a checklist); lightweight evidence only (§6).
- **T2 — Standard**: failure affects one feature or area; recovery is a rollback or quick fix. Mandatory: `checklists/change-review.md`, applied per that checklist's rules — the T2 core subset plus any other checks that clearly apply, not the full list by default.
- **T3 — Significant**: failure affects many users or multiple components, or recovery is costly — migrations, contracts, dependency upgrades, legacy code without a safety net. Mandatory: `checklists/change-review.md` (full applicable list) + `checklists/production-readiness.md`; load the governing reference(s) in full.
- **T4 — Critical**: failure could cause serious harm — financial, legal, personal data, availability, security — or take down the product. Mandatory: every applicable T2/T3 gate plus the security or incident checklist that applies; gate the release; record an independent human review where organizational policy or assessed risk requires it (§6).

**Gates are cumulative.** A higher tier includes every applicable lower-tier gate: T2 runs `checklists/change-review.md`; T3 adds `checklists/production-readiness.md` and the full applicable change-review list; T4 keeps all of those and adds the security or incident checklist that applies. A higher tier never drops a lower-tier gate.

**Escalate one tier** when the change is irreversible or lands in an area without a test safety net.

**Documentation is not automatically trivial.** API docs, compliance records, user-facing text, and audited material classify by the impact of being wrong, exactly like code.

## 2. Paradigm selection (SDD / TDD / DDD / DADD)

Choose the primary paradigm from the task's **dominant risk**. DADD is not a competing primary paradigm — it is an execution/documentation overlay for substantial AI-generated or multi-file work, applied on top of the primary choice.

| Dominant risk | Primary paradigm | Because |
|---|---|---|
| Contract, API, cross-team interface, compliance, or correctness-critical spec | **SDD** — write the contract, input/output schema, error semantics, and acceptance criteria before code | Behavior is fixed by agreement |
| Behavioral regression, subtle bug fixes, refactors that must be provably correct | **TDD** — write the failing test first, then implement, then refactor | Tests are both spec and safety net |
| Business complexity, evolving domain language, multi-team strategic alignment | **DDD** — bounded context, ubiquitous language, aggregates, domain events | The domain, not the framework, drives design |
| No single risk dominates | **Standard workflow** — run §4 without paradigm ceremony | The minimum rule set that protects the risk wins |

Rules:
- **DADD is an overlay, not a fourth primary.** When the task is substantial AI-generated or multi-file work (new features, large migrations, cross-cutting changes), write spec → design → acceptance documents, generate against them, then validate with the primary paradigm's checks. A routine edit performed by an agent is not DADD.
- Correctness paramount → SDD or TDD. Business complexity dominant → DDD. Neither dominates → standard lightweight workflow.
- Never force DDD on generic subsystems; never force TDD on throwaway prototypes.

## 3. Conflict arbitration

When rules from different sources disagree, apply in this order and record the resolution:

1. **Safety over style** — correctness, security, data integrity, and availability beat readability, convention, and performance.
2. **Behavior over structure** — keep behavior changes and structural refactoring in separate, individually verifiable steps. They need not be separate tasks or commits, but each step must build and pass checks, and the behavior change must be identifiable on its own.
3. **Evidence over theory** — measured facts (profiles, load tests, incident data) beat book rules; state the measurement.
4. **Least surprise over cleverness** — on equal evidence, choose the design a maintainer would least expect to break.
5. **Local over global** — a scoped fix that fits the codebase beats a textbook refactor the team will not maintain.
6. **Minimum over maximum** — apply the smallest rule set that protects the risk; do not stack every book onto one function.

Record the conflict and its resolution in the change evidence.

## 4. Workflow

1. **Classify** — risk tier, paradigm, and areas touched.
2. **Load** — the selected references and checklists only.
3. **Specify** — one sentence of intent, the behavior that must change, and the behavior that must stay. For T3/T4, write acceptance criteria before code. If the intended behavior or contract is ambiguous, request clarification before implementing — do not guess.
4. **Design within boundaries** — dependency direction inward, deep modules, information hiding, intention-revealing names; apply the loaded reference rules.
5. **Test** — establish or extend the safety net per the paradigm; characterize legacy areas before changing them.
6. **Implement** — small, verifiable steps; keep behavior changes and refactors separable so each step can be checked; keep cleanup out of behavior steps.
7. **Review** — run the selected checklists against the change; fix every actionable finding.
8. **Verify** — run tests, linters, and the checks the checklists require; collect evidence.
9. **Report** — state tier, paradigm, references used, evidence, and any deferred risk. Never claim commit, push, or release authority (see §6).

## 5. Reference routing

Load only the rows that match the task — 1–3 by default, more only when scope demands it.

| Task area | Load this reference | Also apply (conditional) |
|---|---|---|
| Functions, naming, smells, small refactors, construction discipline, pragmatism | `references/core-codecraft.md` | `checklists/change-review.md` (T2+) |
| New module/service, architecture, boundaries, domain modeling, enterprise patterns | `references/architecture-domain.md` | `checklists/change-review.md` (T2+); `checklists/production-readiness.md` (T3+) |
| Data models, storage, replication, consistency, streams, events, distributed systems | `references/data-distributed.md` | `checklists/change-review.md` (T2+); `references/observability-sre.md` when monitoring is in scope |
| Production hardening/reliability — timeouts, retries, circuit breaking, resource bounds | `references/data-distributed.md` (Harden the runtime) | `checklists/production-readiness.md` (T3+, and T2 where reliability is a stated risk) |
| Shipping, release, deployment, rollback, feature flags | `references/delivery.md` | `checklists/production-readiness.md` (T3+) |
| Monitoring, logging, tracing, SLOs, alerting, on-call | `references/observability-sre.md` | `references/incident-chaos.md` when incident response is in scope |
| Security, auth, input validation, secrets, threat modeling | `references/security.md` | `checklists/security-review.md`; for a security/auth rollout, also `references/delivery.md` + `references/observability-sre.md` when deployment or compatibility is in scope |
| Latency, throughput, profiling, capacity | `references/performance.md` | `references/observability-sre.md` when monitoring is in scope |
| Infrastructure, IaC, CI/CD, environments, containers | `references/infrastructure.md` | `references/delivery.md` when deployment is in scope |
| Process, planning, estimation, review culture, collaboration | `references/team-process.md` | `checklists/change-review.md` (T2+) |
| Incidents, outage response, chaos engineering, resilience | `references/incident-chaos.md` | `checklists/incident-review.md` |
| Compliance, regulation, audit, data retention, records | `references/compliance-audit.md` | `checklists/security-review.md` |

Checklists by gate:
- `checklists/change-review.md` — before declaring any T2+ change complete.
- `checklists/production-readiness.md` — before release of T3/T4.
- `checklists/security-review.md` — before declaring complete any change touching auth, data, or hostile-input surfaces.
- `checklists/incident-review.md` — after an incident or failed rollout, before closing it.

## 6. Evidence, authority, and completion rules

**Authority.** This skill grants no authority to commit, push, merge, or release. Those actions happen only on explicit user instruction and remain the user's decision. Where this skill says "release" or "ship", it means the recommendation and evidence the user decides on.

**Human review.** For a T4 production release, where organizational policy or assessed risk requires it, record an independent human review before release. Produce the full evidence and recommendation locally; flag the required human sign-off as a release condition. Never refuse or stall the analysis itself — a missing reviewer is a stated condition, not a reason to stop work or to block the evidence.

**Evidence scales with tier.**
- **T1 — lightweight evidence only**: the one-sentence intent plus focused verification that the change does what it claims (build, test, or read-back, as applicable). No checklist, no ceremony.
- **T2+ — full evidence set** below.

A T2+ task is complete only when each applicable item has concrete evidence:

- **Intent** — the one-sentence change intent and the preserved behavior are stated.
- **Safety net** — tests written or updated pass, or the verification gap is recorded with its reason.
- **Checks** — every box in the selected checklists is answered; non-applicable items say "N/A: reason".
- **Behavior preservation** — any refactor ran with the same tests green before and after; behavior and structural steps are each verifiable.
- **Risk disposition** — deferred risks, arbitration decisions, and unknowns are listed.
- **No new broken windows** — the touched area is no worse than found. Fix a smell when a reference flags it as blocking; never require gratuitous refactoring or a fixed quota of smells removed.

If evidence cannot be produced (no harness, no environment), state it explicitly and record it as a risk — do not claim completion.

**Completion = (T1) intent + focused verification; (T2+) all selected checklist items satisfied or explicitly waived + evidence recorded; (T4) the required human review recorded where policy or risk demands it. Completion never authorizes commit, push, or release — those await the user.**

## 7. Non-goals

- Not a style guide for a specific language or framework — apply team conventions on top.
- Not a substitute for a security or compliance officer — T4 security/compliance findings escalate to humans.
- Not an excuse to over-engineer — the minimum rule set that protects the risk always wins.

*The source books, standards, and authoritative references are distilled into the `references/` files; this SKILL.md only routes and enforces discipline.*
