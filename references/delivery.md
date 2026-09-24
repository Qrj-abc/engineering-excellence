# Continuous Delivery & DORA — Engineering Guidance

Concise, actionable guidance for shipping software fast and safely. Principles are paraphrased from *Continuous Delivery* (Humble & Farley), *Accelerate* (Forsgren, Humble & Kim), DORA's State of DevOps research, and Google's SRE books — see Sources for the originals. Use this when planning a pipeline, choosing a branching or release strategy, or reviewing why deploys are slow or scary.

## Core model
**Sources:** [cd-book] [accelerate] [dora] [trunk-based]

- **Pipeline over ceremony.** Every change flows through an automated deployment pipeline (build → test → deploy) so releasing is a routine action, not a scheduled event. If a human-run runbook is required to ship, the pipeline is unfinished.
- **Small batches.** Ship small, frequent changes: they are faster to review, test, and roll back, and each one has a smaller blast radius.
- **Trunk-based development (recommendation).** Where the team's branching policy allows it, merge to trunk frequently — daily or more when practical — via short-lived branches. DORA research consistently links long-lived branches and release trains to lower delivery performance. Merging is a user/team decision, not an action this skill performs.
- **Version control everything.** Source, configuration, database migrations, and pipeline definitions all get the same review, history, and audit trail.
- **Quality is built in.** Automated tests at every level run inside the pipeline; teams that bake testing into the workflow outperform teams that add it after the fact.
- **Release is boring.** Deploys should be so routine that nobody celebrates them; drama on deploy day is a symptom.

## Decision rules
**Sources:** [cd-book] [accelerate] [feature-toggles]

- Where trunk-based development is in use, merge as soon as the change passes CI and review — do not wait for a release date; the merge itself is executed by the user/team, not by this skill.
- Use feature flags for risky or uncertain work: deploy the code often, flip the flag when ready, and prune flags on a schedule to avoid flag sprawl.
- On a production incident, roll back first when the faulty change is identifiable and rollback is fast; forward-fix only when rollback is slower than the fix or the fix is trivial.
- Add manual approval gates only where regulation or compliance demands them — every gate is a throughput tax and a handoff point.
- Treat flaky tests as defects: fix or delete them before merging more, or a green CI no longer protects anything.
- Automate the slowest manual step first; removing one handoff improves lead time more than a faster build.

## Security-policy rollout
**Sources:** (synthesis)

Ordinary feature flags hide work-in-progress from users; security-policy rollouts — authorization rules, token validation, access scope — change who can do what, so a wrong default can grant or deny access at scale. Treat them differently:

- Treat an authorization change as a policy rollout, not a feature toggle: it deserves the same review, testing, and rollback discipline as a data migration.
- Run the new policy in shadow or audit mode where safe: evaluate alongside the old policy, log the decisions it would have made, and compare before enforcing anything.
- Set an explicit compatibility policy: state which principals, roles, and token versions the new policy accepts, rejects, or treats as unknown — and prefer reject-with-reason over silent accept for unknowns.
- Enforce in stages: start with the least-risk population or surface, watch allow/deny telemetry, and expand only when the numbers match expectation.
- Emit deny-reason telemetry: make each denial explainable — which rule, which claim, which resource — so policy bugs surface as data rather than support tickets.
- Fail safe: when the policy engine is uncertain or unavailable, default to the outcome that preserves the security boundary, and make that default visible.
- Design rollback so it cannot silently reopen access: rolling back the policy must restore the prior deny set and re-run the audit comparison — a rollback that loosens access is a new incident.

## Tradeoffs
**Sources:** (synthesis)

- Trunk-based vs. long-lived branches: continuous integration and fast feedback vs. isolation — long branches trade today's merge pain for tomorrow's.
- Feature flags vs. branches: flags keep the trunk shippable but accumulate; branches isolate work but defer integration.
- Full continuous delivery vs. staged environment gates: velocity vs. audit certainty — automate whatever the gate exists to check.
- Monorepo vs. polyrepo: atomic cross-service changes vs. independent versioning, access control, and team autonomy.

## Failure modes
**Sources:** (synthesis)

- Deployment theater: long runbooks, deploy-day rituals, and release freezes used to compensate for an incomplete pipeline — the freeze is a symptom, not the problem; the missing automation is.
- Disabled or flaky tests: a green pipeline that no longer guards regressions.
- Metric gaming: quotas on deployment frequency or MTTR get met on paper while quality slips — treat the four keys as measures, not targets.
- Configuration drift between environments: "works in staging" breaks in production because configs were never versioned or tested.
- Unmonitored releases: shipping without health checks, so bad deployments surface only through customer complaints.
- Big-bang migrations: large schema or data changes that cannot be rolled back safely — split them into reversible steps.

## Evidence / verification
**Sources:** [dora] [dora-reports]

- Measure the DORA four keys — deployment frequency, lead time for change, change failure rate, time to restore — continuously, and benchmark against DORA's performance clusters rather than arbitrary goals.
- Verify pipeline health: CI duration, flake rate, deploy success rate, and time from commit to production.
- Run a release drill: an on-call engineer should be able to deploy a small fix to production within minutes, with rollback one action away.
- After each major incident, re-run a postmortem review to confirm follow-up actions actually shipped.

## Sources

- [dora] DORA — the four key metrics: https://dora.dev/guides/dora-metrics-four-keys/
- [dora-reports] DORA — State of DevOps reports: https://dora.dev/publications/
- [cd-book] *Continuous Delivery* (Humble & Farley) companion site: https://continuousdelivery.com/
- [accelerate] *Accelerate* (Forsgren, Humble & Kim) (book; no open edition)
- [trunk-based] Trunk Based Development: https://trunkbaseddevelopment.com/
- [feature-toggles] M. Fowler, "Feature Toggles (aka Feature Flags)": https://martinfowler.com/articles/feature-toggles.html
- [sre-release-eng] Google SRE book — Release Engineering: https://sre.google/sre-book/release-engineering/
- [sre-canarying] Google SRE Workbook — Canarying Releases: https://sre.google/workbook/canarying-releases/
- [sre-postmortem] Google SRE book — Postmortem Culture: https://sre.google/sre-book/postmortem-culture/

Slug definitions, verification status, and per-section mapping: `references/source-map.md`.
