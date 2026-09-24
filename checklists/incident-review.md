# Incident Review Checklist

Post-incident review: reconstruct what happened, bound the damage, identify causal and contributing factors, and close the gaps that let it happen. Blameless by construction — the review targets systems, processes, and action items, never individuals. Follows references/incident-chaos.md.

**Sources:** transitive — items derive from `references/incident-chaos.md`; resolve the chain through `references/source-map.md`. No primary source is cited directly — do not attribute a checklist item to Google SRE or a book.

## Applicability

Run after any production incident (user-facing outage, data loss, security event, SLO breach). Tier by impact, never by cause — the skill's T1–T4 scale:

| Tier | Incident class | Depth |
|---|---|---|
| T1 | Minor blip: auto-recovered or under threshold, no user impact | Timeline review: detection, recovery, one-line cause, follow-up |
| T2 | Significant: user impact, manual recovery | Full review: timeline, causal factors, owned action items |
| T3 | Severe: data loss, security incident, external visibility | Full + formal writeup and external communications |
| T4 | Critical: prolonged outage, compliance or legal scope | Full + formal analysis, evidence preservation, regulatory notification |

Process depth scales with severity: a T1 blip does not warrant the full role structure or writeup of a T3/T4 event.

## Required evidence

- Incident timeline: detection, escalation, mitigation, recovery with timestamps and the systems/roles involved — the skeleton of the review, per references/incident-chaos.md.
- Raw evidence: monitoring/alert history, error and application logs, the exact deploy/commit associated with onset.
- Mitigation record: what was done to contain and recover, and the verification that recovery actually succeeded.
- Communication record: status updates, escalation path, external notification if any.
- Quantified impact: duration, affected users/surfaces, SLO/error-budget consumption, data loss (or confirmed none).

## Decision / blocker criteria

- The review is complete when: causal and contributing factors are identified (or the analysis is explicitly bounded as unknowable with the reason), impact is quantified, and every action item has an owner, due date, and verification method.
- **Action items are blockers to closure, not to service:** each item that prevents recurrence (fix, test, monitoring gap, runbook gap) is tracked to verified closure; the review is reopened if the same signature recurs.
- Security incidents follow the security policy for notification and evidence preservation before this checklist runs.
- **Pass:** writeup recorded, action items tracked in the work tracker, follow-up review date set.

## N/A behavior

`N/A` means the condition genuinely does not apply to this incident, with a one-line reason grounded in the timeline (e.g., "no data was written in this window"). Unknown or unverified is a gap, not `N/A`. On detection, recovery, or action-item checks, `N/A` is almost always a gap to record rather than a pass — prefer stating the gap explicitly (e.g., "no alert existed for this signal" is IR-02, not N/A).

## Checks

- [ ] **IR-01 Declared early.** If anyone wondered whether it was an incident, it was declared; under-declaration is recorded as a gap, per references/incident-chaos.md.
- [ ] **IR-02 Detection was timely.** The signal that fired (or should have) is identified; time-to-detection is stated.
- [ ] **IR-03 Alerts worked or the gap is named.** If nothing alerted, the missing monitor is an action item, not an excuse.
- [ ] **IR-04 Response roles match severity.** For larger incidents, one incident commander coordinates and does not debug, with ops, comms, and scribe assigned to named people; smaller incidents may run with fewer roles — per references/incident-chaos.md.
- [ ] **IR-05 Stabilize-first obeyed.** Rollback, feature flag, or failover preceded cause investigation; every action was logged with timestamps; no undocumented changes.
- [ ] **IR-06 Mitigation was correct and verified.** Recovery steps match the incident; post-recovery verification (smoke, data checks) is recorded, not assumed.
- [ ] **IR-07 Blast radius bounded.** The affected surface, users, and duration are stated; containment steps are explicit.
- [ ] **IR-08 Causal factors are evidence-based.** Causal and contributing factors are traced to evidence (commit, configuration, resource, event), not speculation; a single root cause is not forced where multiple factors contributed; alternative hypotheses are ruled out or noted.
- [ ] **IR-09 Contributing factors listed.** Environmental, configuration, and process factors that amplified the incident are separated from the direct cause.
- [ ] **IR-10 Data integrity confirmed.** Data loss, corruption, or duplication is confirmed or explicitly ruled out, with the evidence.
- [ ] **IR-11 Impact quantified.** Duration, user impact, SLO/error-budget consumption, and cost are in the writeup.
- [ ] **IR-12 Blameless throughout.** The writeup and review target systems and processes; no personal attribution, per references/incident-chaos.md.
- [ ] **IR-13 Communications adequate.** Status updates went out at an appropriate cadence to the right audience; gaps become action items, per references/incident-chaos.md.
- [ ] **IR-14 Evidence preserved and notification handled.** For T3/T4: logs, sessions, and artifacts were preserved before changes; legal/compliance notification follows policy where required.
- [ ] **IR-15 Regression coverage added.** A test or check that fails on this incident's signature is added, or its absence is justified.
- [ ] **IR-16 Monitoring gap closed.** Every blind spot that delayed detection now has a monitor or a documented decision not to monitor.
- [ ] **IR-17 Runbook updated.** The runbook reflects what actually works for this class of incident.
- [ ] **IR-18 Docs and decision records updated.** Where the incident contradicts documented behavior, the documentation is corrected in the same effort.
- [ ] **IR-19 Action items are actionable.** Each has a named owner, due date, and verification method; no owner means no item, per references/incident-chaos.md.
- [ ] **IR-20 Postmortem written promptly and archived.** Written while memory is fresh (within days, not weeks), shared according to the organization's security, privacy, and legal rules, then archived as frozen history; a follow-up review date is set — per references/incident-chaos.md.
