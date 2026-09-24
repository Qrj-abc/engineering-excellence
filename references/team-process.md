# Team Process — Topologies, Decisions, and Debt

Operating rules for team shape, architecture decisions, and technical debt. Distilled from Team Topologies, ADR practice, and Fowler's debt model — not a substitute for the originals.

## 1. Team Topologies (Skelton & Pais)
**Sources:** [team-topologies]
**Core bias:** team shape follows software boundaries and cognitive load, not org charts or tech layers.
### Team types
**Sources:** [team-topologies]
- **Stream-aligned** — owns one stream of work end-to-end; the default team type.
- **Platform** — serves internal teams with products; measured by adoption and time-to-value, not output.
- **Enabling** — transfers capability (practices, tooling) then leaves; always time-boxed.
- **Complicated-subsystem** — owns a genuinely complex specialist area (e.g., payment engine); justify with real complexity.
### Decision rules
**Sources:** [team-topologies]
- Default to stream-aligned; create other types only for a named problem (specialization, shared platform, skill gap).
- Budget cognitive load: if a team can't hold its systems in mind, split the stream or build a platform — don't grow the team.
- Choose interaction modes deliberately: **collaboration** (short bursts on a shared problem), **X-as-a-service** (consume APIs), **facilitation** (enabling). Time-box collaboration.
- Design team APIs (docs, interfaces, ownership map) so others interact without interrupting.
- Align team boundaries with bounded contexts and deployment units; minimize handoffs.
### Ownership
**Sources:** [team-topologies]
- Each team owns its stream end-to-end as far as is practical: code, run, observability, on-call, and its decisions — with explicit, agreed exceptions (shared platforms, compliance-gated deploys, temporary enablement) rather than split ownership by default.
- Every platform capability and enabling engagement has one named owner and an explicit end date.
### Stop conditions and anti-patterns
**Sources:** [team-topologies]
- Stop when the org chart starts mirroring tech layers (frontend team, DB team) instead of streams.
- Never let collaboration become permanent — convert to X-as-a-service once the pattern stabilizes.
- Avoid split ownership ("team A builds, team B deploys") as a default — it breaks the stream; where it is unavoidable (compliance, specialist tooling), make the boundary and handoffs explicit and owned.
- No vanity complicated-subsystem teams; they slow everyone else.
### Evidence
**Sources:** [team-topologies]
- Topology map (types + interactions) reviewed on a cadence matched to org and architecture change rate against friction: cycle time, handoff count, on-call load, DORA metrics.

## 2. ADR Practice — Architecture Decision Records
**Sources:** [adr] [cognitect-adr]
**Core bias:** unrecorded decisions get re-litigated; decisions without consequences aren't decisions.
### Decision rules (Context → Decision → Consequences)
**Sources:** [adr] [cognitect-adr]
- **Write when** the call is hard to reverse, expensive, or changes a cross-team contract or architecture direction; skip routine, reversible choices (a commit message suffices).
- State **context** (forces, live options), **decision** (one imperative sentence + status: Proposed/Accepted/Superseded), **consequences** (what gets easier, what gets harder, one alternative considered and why it lost).
- Decide at the last responsible moment — collect facts before committing.
- Store ADRs next to the code in a conventional per-repo location (e.g., `docs/adr/`), versioned with it; keep the location stable within a repo rather than prescribing one global path; supersede via a new ADR, never by editing.
### Ownership
**Sources:** [adr] [cognitect-adr]
- The team whose system is affected owns the ADR; approval scales with impact — a single named approver is a fine default, while high-impact or cross-team decisions need sign-off from the affected teams.
- Accepted ADRs link to implementation and are revisited on a cadence that matches how fast the architecture changes (quarterly is a common default, not a requirement).
### Stop conditions
**Sources:** [adr] [cognitect-adr]
- Done when merged with status, decision, and consequences — reopen only via a superseding ADR.
- Stop when ADRs become ceremony (rubber-stamped reviews); raise the bar for what deserves one.
### Anti-patterns
**Sources:** [adr] [cognitect-adr]
- Retrospective-only ADRs (decision without context); zombie ADRs (accepted, never implemented); ADR-as-blog (every thought documented).
### Evidence
**Sources:** [adr] [cognitect-adr]
- A decision log with a supersede chain — a new engineer can answer "why is it like this?" from ADRs alone.

## 3. Technical Debt Management
**Sources:** [fowler-debt-quadrant] [fowler-debt]
**Core bias:** debt is a deliberate, priced trade-off — bugs and carelessness are defects, not debt.
### Decision rules
**Sources:** [fowler-debt-quadrant] [fowler-debt]
- Classify with Fowler's quadrant (prudent/reckless × deliberate/inadvertent); only deliberate, prudent, time-boxed shortcuts count as acceptable debt.
- Record per item: what was deferred, why, **principal** (pay-down effort), and an **interest** estimate (added change time, incident risk) — quantify where you can, estimate where you cannot, and say which.
- Pay interest first: when debt slows changes or correlates with incidents, schedule principal payment.
- Boy-scout rule: when you touch a debt-laden area, reduce its debt as part of the change.
- Cap debt per area where possible; an item with no owner and no estimate is an orphan, not debt.
### Ownership
**Sources:** [fowler-debt-quadrant] [fowler-debt]
- The incurring team owns the debt and its register entry; the register is reviewed each planning cycle; critical debt (security/availability) gets an SLA.
### Stop conditions
**Sources:** [fowler-debt-quadrant] [fowler-debt]
- Stop carrying when interest clearly exceeds principal — paying down is likely the cheaper path; treat the comparison as a decision input, not a precise accounting.
- Close an item only with evidence of payment (implementation verified by tests — merge status is not required) or explicit retirement; never close by ignoring.
### Anti-patterns
**Sources:** [fowler-debt-quadrant] [fowler-debt]
- Invisible debt (unrecorded shortcuts surfacing in incidents); "we'll fix it in the rewrite" (rewrites rarely happen — fix incrementally); debt-as-excuse for known-broken behavior; debt holidays (paying down everything at once without measuring interest).
### Evidence
**Sources:** [fowler-debt-quadrant] [fowler-debt]
- Register with date, owner, principal, interest, review date; change-time and incident trends validate the interest estimates.

## Sources

- [team-topologies] *Team Topologies* (Skelton & Pais) — key concepts: https://teamtopologies.com/key-concepts
- [adr] Architecture Decision Records: https://adr.github.io/
- [cognitect-adr] M. Nygard, "Documenting Architecture Decisions": https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions
- [fowler-debt-quadrant] M. Fowler, "TechnicalDebtQuadrant": https://martinfowler.com/bliki/TechnicalDebtQuadrant.html
- [fowler-debt] M. Fowler, "TechnicalDebt": https://martinfowler.com/bliki/TechnicalDebt.html

Slug definitions, verification status, and per-section mapping: `references/source-map.md`.
