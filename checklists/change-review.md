# Change Review Checklist

Pre-approval review of a code change before integration. Run by the author before declaring the change complete, re-verified by a reviewer; the review produces evidence and a pass/block recommendation — integration or merge remains the user's or team's decision. Applies to source, configuration, migrations, pipeline definitions, and documentation changes.

**Sources:** transitive — items derive from `references/delivery.md` and `references/team-process.md`; resolve the chain through `references/source-map.md`. No primary source is cited directly — do not attribute a checklist item to OWASP, NIST, or a book.

## Applicability

Tier by blast radius and reversibility, not by confidence — the skill's T1–T4 scale:

| Tier | Change class | Checklist use |
|---|---|---|
| T1 | Mechanical or trivial: docs, formatting, renames, test-only additions; no behavior change | No checklist — a direct diff review suffices |
| T2 | Normal: feature, bugfix, moderate configuration | Lightweight core subset (below) plus any other checks that obviously apply |
| T3 | Significant: new service or capability, cross-cutting seams, schema/data migration, security-sensitive surface | Full applicable checklist + independent reviewer; escalate to checklists/security-review.md |
| T4 | Critical: credentials/auth at scale, data-loss potential, public production infrastructure, compliance scope | Full applicable checklist + adversarial pass and formal sign-off |

Escalate T2 → T3 when the change touches credentials, sandboxing/isolation, network exposure, or untrusted input handling.

**T2 core subset:** CR-01, CR-02, CR-05, CR-07, CR-11, CR-12, CR-13, CR-15, CR-16, CR-19, CR-20. Add any other check that clearly applies to this change; do not force the full list.

## Required evidence

Evidence is produced by the change, not claimed about it:

- The exact diff and the files it touches; clean, reviewable scope (small batches — see references/delivery.md where delivery practice applies).
- A summary of the semantic surface: interfaces, events, registrations, configuration, and data formats affected.
- Output of the relevant automated checks actually run (unit tests, typecheck, lint, snapshot/golden files, build) — a check that was not run is not evidence.
- A decision record (ADR) where the change makes a durable, cross-boundary, or hard-to-reverse decision (see references/team-process.md for ADR practice).
- Docs, README, and interface comments updated in the same change wherever behavior, defaults, or errors change.

## Decision / blocker criteria

- **Block (selected checks):** broken required behavior; violated interface/lifecycle/ownership contract; missing required evidence; missing diagnostics or audit where an operational or security need requires it; docs contradicting code; missing decision record where a durable, cross-boundary, or hard-to-reverse decision was made; a security-sensitive surface not cross-flagged to checklists/security-review.md.
- **Defer (track, don't block):** style nits, optional refactors, follow-up tests that assert nothing new today.
- **Escalate, don't veto:** disagreement with a decision record is a design discussion with its owner, not an automatic block.
- **Pass:** every selected check is `PASS` or justified `N/A`; the decision is recorded with the reviewer's name.

## N/A behavior

`N/A` applies only to checks actually selected and run. It means the condition genuinely does not apply to this change, with a one-line reason (e.g., "no resource acquisition", "no network paths"). Unknown or unverified is a gap, not `N/A`. Checks not selected (the rest of the full list at T2) are simply not marked — do not fill them with `N/A`.

## Checks

- [ ] **CR-01 Intent matches implementation.** Trace each changed behavior to the ticket or decision record it claims to serve; flag implementation that contradicts stated intent.
- [ ] **CR-02 Both sides of every interface.** Verify caller and callee agree on parameters, errors, cancellation, ownership, and disposal for every changed interface.
- [ ] **CR-03 Resource lifecycle.** Every acquired resource (subscription, listener, registration, handle, connection) has a documented release path that runs on success and failure; teardown does not leak.
- [ ] **CR-04 Lifecycle and concurrency.** For async setup, callbacks, background work, or teardown: check publication races, cancellation during awaits, callback containment, reentry, and quiescent shutdown.
- [ ] **CR-05 Error paths are real.** Every denial/error path reaches the operation that handles it; alternate callers cannot bypass validation, guards, or wrapper ordering.
- [ ] **CR-06 Boundary and consumer fit.** Trace current consumers; flag consumer-specific behavior leaking into generic components and speculative API expansion for a single caller.
- [ ] **CR-07 Scope and necessity.** Map each abstraction, option, and compatibility path to a current contract and consumer; challenge speculative generality.
- [ ] **CR-08 Configuration choices are evidenced.** Each new default, public operation, or format cites current-consumer evidence or prior art; otherwise it is an explicit choice or deferral.
- [ ] **CR-09 Diagnostics and audit.** Where an operational or security need requires it (debugging, replay, audit), sufficient logs, metrics, or audit records exist to diagnose the behavior; no silent behavior changes.
- [ ] **CR-10 State ownership is explicit.** Each retained value's ownership (borrowed vs owned) matches the contract; caches, derived state, and query views trace to the authoritative source (data-system guidance applies where derived data exists).
- [ ] **CR-11 Bounds cover the final operation.** Probe tiny/exact limits, oversized inputs, and multibyte or Unicode text where size limits exist, at the owner of the complete emitted result.
- [ ] **CR-12 Real entry path is tested.** Tests exercise the shipped entry point (CLI, service boundary, worker, library API), not hand-assembled internals that bypass it.
- [ ] **CR-13 Tests fail on the intended regression.** Assertions verify external state, side effects, or — for pure functions — outputs and return values, rather than restating the implementation; green coverage numbers are not correctness evidence.
- [ ] **CR-14 Invariants are asserted semantically.** Where invariants exist, checks assert observable state, relationships, or input/output properties (property-based tests suit pure functions), not implementation presence; a deliberately invalid case fails through the real path.
- [ ] **CR-15 Output and behavior diffs are reviewed as behavior.** Expected-output changes (snapshots, fixtures, golden files) are reviewed as behavior changes, not formatting noise.
- [ ] **CR-16 Docs match code in the same change.** Config, defaults, errors, wire fields, and events update README, docs, and interface comments; comments state non-obvious contracts only.
- [ ] **CR-17 Decision records match shipped reality.** Where a decision record exists, the implementation matches it or the record is explicitly superseded; no zombie ADRs (ADR practice in references/team-process.md).
- [ ] **CR-18 Boundary typing and validation.** Where the language's type system supports it, opaque cross-boundary identifiers are typed rather than bare strings; validation happens at trust boundaries, not redundantly in typed same-process code.
- [ ] **CR-19 No dead weight.** No commented-out code, no unlabeled TODO/FIXME introduced, no duplicated rationale; the touched area is left better or explicitly contained.
- [ ] **CR-20 Security surface flagged.** Any touch to credentials, authentication, network, subprocess/shell, or untrusted input is cross-referenced into checklists/security-review.md before the review passes.
