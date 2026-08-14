# Observability & SRE — Engineering Guidance

Concise, actionable guidance for monitoring, SLOs, alerting, and on-call practice. Principles are paraphrased from Google's SRE book and Workbook and *Observability Engineering* (Majors, Fong-Jones, Miranda) — see Sources for the originals. Use this when defining SLOs, building alerting, or designing what a service must emit.

## Mental model

- **Reliability is a product decision.** Perfect reliability costs more than it is worth; choose the amount of unreliability you can tolerate, and budget for it explicitly. (SRE book: "Embracing Risk".)
- **Monitoring vs. observability.** Monitoring checks known failure states with dashboards and thresholds; observability means the system's outputs — structured, high-cardinality events — let you answer questions you did not know to ask. (Observability Engineering.)
- **Signals are lenses, not pillars.** Logs, metrics, and traces answer different questions; pick the signal that fits the question, and enrich events with context (users, features, services) instead of hoping one tool covers everything. (Honeycomb.)

## SLO / error budget decision rules

- Define SLIs from user-visible behavior — e.g., successful requests and their latency — not from internal health like CPU or queue depth.
- Set SLO targets from business impact and risk tolerance: 99.9% permits roughly 43 minutes of downtime per month, 99.99% about 4 minutes; each added nine multiplies cost and operational complexity.
- Error budget = 100% − SLO. Spend it deliberately on launches and experiments; when it is exhausted, stop risky releases until it recovers.
- Write the budget down in a service SLO document and enforce it, or it becomes decoration.
- Start with one or two SLOs for the most critical user journeys; every additional SLO is ongoing maintenance.
- Page on error-budget burn rate, not on raw thresholds: alert only when the current rate would exhaust the budget sooner than is acceptable. (SRE Workbook: "Alerting on SLOs".)
- Gate promotion of a new service on having an SLO, golden-signal dashboards, and an on-call owner.

## Alerting decision rules

- Page only when a human must act right now; otherwise send to a dashboard, a ticket, or a log. (SRE book: "Practical Alerting".)
- Cover the golden signals — latency, traffic, errors, saturation — per service. (SRE book: "Monitoring Distributed Systems".)
- Every page needs an owner, a runbook, and a proven way to fire; an alert without an action is noise.
- Prefer a few high-signal pages; alert fatigue is itself a reliability failure.

## Tradeoffs

- Event cardinality vs. cost: high-cardinality events answer novel questions but cost more to store and index — keep full fidelity for errors and traces, sample or aggregate high-volume metrics.
- Centralized vs. per-team tooling: a shared platform improves discoverability; per-team tools improve autonomy — standardize the transport and query language, not necessarily the UI.
- Strict SLOs vs. feature velocity: a tight error budget gates risky releases — loosen it when risk tolerance is genuinely higher.
- Metrics vs. events: metrics are cheap and pre-aggregated but only answer known questions; events are rich and queryable but more expensive.
- Toil vs. automation: automate what repeats; keep human judgment for the judgment calls.

## Failure modes

- Alert fatigue: too many pages lead to muted alerts and missed real incidents.
- Dashboard wallpaper: charts nobody reads, thresholds nobody remembers.
- Wrong SLI: SLOs measured on internal health drift from what users actually experience.
- Unenforced budgets: SLOs that never block anything have no teeth.
- Blame-oriented postmortems with no tracked follow-ups, so the same incident recurs. (SRE: "Postmortem Culture".)
- Silent observability gaps: critical paths with no telemetry, discovered only during an outage.

## Evidence / verification

- Implement burn-rate alerts and verify they fire before users complain — e.g., with synthetic probes and canary traffic.
- Run game days / failure drills periodically to prove on-call response works when it matters. (SRE book: "Testing Reliability".)
- Audit every postmortem for concrete, tracked action items with owners; measure recurrence of incident classes over time.
- Verify observability quality directly: an on-call engineer should be able to answer "why is latency up right now" end to end, in minutes, from the tools.
- Hold a regular reliability review that reads burn rates, toil, and incident trends — not just dashboards.

## Sources

- Google SRE books (free, official): https://sre.google/books/
- SRE book — Embracing Risk: https://sre.google/sre-book/embracing-risk/
- SRE book — Service Level Objectives: https://sre.google/sre-book/service-level-objectives/
- SRE book — Monitoring Distributed Systems: https://sre.google/sre-book/monitoring-distributed-systems/
- SRE book — Practical Alerting: https://sre.google/sre-book/practical-alerting/
- SRE Workbook — Implementing SLOs: https://sre.google/workbook/implementing-slos/
- SRE Workbook — Alerting on SLOs: https://sre.google/workbook/alerting-on-slos/
- SRE Workbook — On-Call: https://sre.google/workbook/on-call/
- SRE book — Postmortem Culture: https://sre.google/sre-book/postmortem-culture/
- SRE book — Testing Reliability: https://sre.google/sre-book/testing-reliability/
- *Observability Engineering* (O'Reilly): https://www.oreilly.com/library/view/observability-engineering/9781492076438/
- OpenTelemetry — signals overview: https://opentelemetry.io/docs/concepts/signals/
- Honeycomb — "They Aren't Pillars, They're Lenses": https://www.honeycomb.io/blog/they-arent-pillars-theyre-lenses/
