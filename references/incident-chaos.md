# Incidents, Chaos, and Reliability — Operating Rules

Rules for chaos experiments, incident response, blameless postmortems, and SLO-driven reliability. Distilled from Principles of Chaos, Google SRE (book + workbook), and postmortem culture — not a substitute for the originals.

## 1. Chaos Engineering
**Sources:** [principles-of-chaos] [chaos-eng]
**Core bias:** inject failures deliberately, as controlled experiments, before production does it for you.
### Principles (Principles of Chaos)
**Sources:** [principles-of-chaos] [chaos-eng]
- Form a **steady-state hypothesis**: define measurable normal behavior (an SLI) up front.
- Vary real-world events (failures, latency, traffic spikes) — conditions users actually experience.
- **Automate** experiments; keep blast radius minimal; run in production only when safe — otherwise start with game days and staging.
- Every experiment has defined scope, duration, and rollback.
### Decision rules
**Sources:** [principles-of-chaos] [chaos-eng]
- No hypothesis, no experiment: "if X fails, the system stays within SLO because of Y."
- One experiment at a time: baseline → inject → measure → compare.
- Gate production experiments on proven observability, alerting, rollback, and on-call coverage.
- Use game days to rehearse teams; use experiments to validate hypotheses.
### Ownership
**Sources:** [principles-of-chaos] [chaos-eng]
- The service team owns its experiments; a reliability owner approves the plan and blast radius.
- On-call can abort any experiment at any time — abort wins.
### Stop conditions
**Sources:** [principles-of-chaos] [chaos-eng]
- Stop when the hypothesis is confirmed or refuted; record the verdict either way.
- Abort if blast radius exceeds the plan, steady state is lost beyond tolerance, or a real incident starts.
### Anti-patterns
**Sources:** [principles-of-chaos] [chaos-eng]
- Chaos without hypothesis or measurement; unattended experiments; production testing without rollback or observability; declaring "we're resilient" from one successful run.
### Evidence
**Sources:** [principles-of-chaos] [chaos-eng]
- Per experiment: hypothesis, baseline, injection, observed metrics, verdict, follow-up actions.

## 2. Incident Response
**Sources:** [sre-managing-incidents] [pagerduty-ir]
**Core bias:** during an incident, stabilize and communicate — root cause is for after, not during.
### Decision rules
**Sources:** [sre-managing-incidents] [pagerduty-ir]
- **Declare early**: if you wonder whether it's an incident, it is; under-declaring is the most common failure.
- Severity by impact (users, money, compliance, SLO breach), never by cause — a one-line config typo can be Sev-1.
- For Sev-1/large incidents assign one **incident commander (IC)** who coordinates and does not debug, plus ops lead (fixing), comms lead (updates), and scribe (timeline); scale roles down with severity — a small incident may need only a lead and a timeline.
- Stabilize first: mitigate before investigating root cause, but choose the mitigation for the situation — rollback, feature flag, failover, rate limit, scaling, or deliberate degradation; the cheapest safe option wins.
- Log actions with timestamps best-effort during the response (a scribe or a shared doc suffices; do not block fixes on logging); fixed-interval status updates even when there is nothing new.
### Ownership
**Sources:** [sre-managing-incidents] [pagerduty-ir]
- Where roles are staffed, the IC owns the response until closure or handoff and every role is a named person; the service team owns follow-ups.
### Stop conditions
**Sources:** [sre-managing-incidents] [pagerduty-ir]
- Over when the service is restored to an acceptable level (meets SLO) — not when root cause is found.
- Close when mitigated, users informed, timeline recorded, and a postmortem is scheduled.
### Anti-patterns
**Sources:** [sre-managing-incidents] [pagerduty-ir]
- Hero debugging (a silent lone fixer); fixing without understanding (the same incident recurs); no scribe or timeline; declaring victory on partial mitigation.
### Evidence
**Sources:** [sre-managing-incidents] [pagerduty-ir]
- Timeline of all actions (who, when), mitigation actions, declared severity, and closure criteria.

## 3. Blameless Postmortems
**Sources:** [sre-postmortem] [atlassian-postmortem]
**Core bias:** the goal is learning and system change, never punishment; people did their best with what they knew.
### Decision rules
**Sources:** [sre-postmortem] [atlassian-postmortem]
- Write while memory is fresh — commonly within 48–72 hours; scale the deadline to severity (sooner for Sev-1s) and never let delay become a reason to skip it. The incident timeline is the skeleton.
- Ask "what in the system allowed this?" — contributing factors, not "who did it."
- Use the analysis method that fits (5 Whys, contributing-factor analysis, timeline review, causal-loop); stop at the first changeable system property.
- Every action item needs a named owner, a due date, and a way to verify completion; no owner → no item.
- Share openly and keep searchable where security, privacy, and legal rules allow; a secret postmortem teaches nobody, but redact what must be redacted (customer data, secrets, legal holds) and share what remains.
### Ownership
**Sources:** [sre-postmortem] [atlassian-postmortem]
- Prefer a facilitator who was not an incident responder, but whoever runs the session must be neutral; named owners track items to closure (followed up in retrospectives).
### Stop conditions
**Sources:** [sre-postmortem] [atlassian-postmortem]
- Done when action items are filed and tracked, not when the document is written.
- Stop the analysis when causes stop being actionable; "human error" is a starting point, never a conclusion.
### Anti-patterns
**Sources:** [sre-postmortem] [atlassian-postmortem]
- Blame and scapegoating (kills future reporting); action items without owners or dates; "we already knew this" with no change; rushing to a single root cause when multiple factors contributed.
### Evidence
**Sources:** [sre-postmortem] [atlassian-postmortem]
- Postmortem doc (timeline, contributing factors, actions) plus tracked items with closure status.

## 4. SLO Enforcement in an Incident Context
**Sources:** [sre-slo] [sre-alerting-slos]
**Core bias:** reliability is a product decision made with numbers; 100% availability is an anti-goal.
### Decision rules
**Sources:** [sre-slo] [sre-alerting-slos]
- Apply the error-budget policy your organization explicitly adopted — SLI definition, SLO targets, burn-rate alerting, and gating mechanics live in `references/observability-sre.md`; do not restate them here.
- In an incident, the budget is response input: quantify SLO/error-budget consumption in the review, and let the adopted policy decide whether releases gate or halt until the budget recovers or the SLO is renegotiated.
- Reopen or renegotiate SLOs only when the data supports it, on the cadence the adopted policy sets — do not invent defaults here.
### Ownership
**Sources:** [sre-slo] [sre-alerting-slos]
- The service owner agrees SLOs with product; where gating is policy, on-call has authority to invoke it when the budget is burned.
### Stop conditions
**Sources:** [sre-slo] [sre-alerting-slos]
- Where gating is policy, gate releases when burn rate exceeds the threshold for the window; resume when the budget recovers or the SLO is renegotiated.
- Stop adding SLOs nobody monitors — an unmonitored SLO is a lie.
### Anti-patterns
**Sources:** [sre-slo] [sre-alerting-slos]
- Chasing 100%; vanity SLIs (metrics uncorrelated with user pain); error budgets never enforced (gates must be real); SLOs set without product agreement.
### Evidence
**Sources:** [sre-slo] [sre-alerting-slos]
- Incident review records budget consumption at onset and recovery; release-gate actions taken under the adopted policy.

## Sources

- [principles-of-chaos] Principles of Chaos: https://principlesofchaos.org/
- [chaos-eng] *Chaos Engineering* (O'Reilly): https://www.oreilly.com/library/view/chaos-engineering/9781492043850/
- [sre-managing-incidents] Google SRE book — Managing Incidents: https://sre.google/sre-book/managing-incidents/
- [pagerduty-ir] PagerDuty Incident Response: https://response.pagerduty.com/
- [sre-postmortem] Google SRE book — Postmortem Culture: https://sre.google/sre-book/postmortem-culture/
- [atlassian-postmortem] Atlassian — Blameless Postmortems: https://www.atlassian.com/incident-management/postmortem/blameless
- [sre-slo] Google SRE book — Service Level Objectives: https://sre.google/sre-book/service-level-objectives/
- [sre-alerting-slos] Google SRE Workbook — Alerting on SLOs: https://sre.google/workbook/alerting-on-slos/

Slug definitions, verification status, and per-section mapping: `references/source-map.md`.
