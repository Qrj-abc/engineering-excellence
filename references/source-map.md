# Source Map — Fine-Grained Provenance for `references/`

Every functional section in this skill's eleven `references/` files carries a one-line
`**Sources:**` tag directly under its heading, and every reference ends with a `## Sources`
block whose entries are keyed by slug. This file is the ledger that resolves those slugs to
full citations, states when each link was last checked, records the granularity of each
file, and marks which sections are original synthesis rather than distilled material.

## Coverage at a glance

| Layer | Files | Sections | Attribution |
|---|---|---|---|
| References | 11 | 109 leaf sections | `**Sources:**` per leaf section — full coverage |
| `checklists/` | 4 | 20 sections | Transitive only: a `**Sources:**` line naming the references each checklist draws on |
| `SKILL.md` | 1 | 8 sections | None by design — orchestration is `(synthesis)` |
| `README.md`, this file | 2 | — | Documents, not rules |

## Placement convention

- **Leaf sections carry the tag.** A leaf is a section with its own content (bullets or prose directly under the heading). The tag goes on the line immediately below the heading.
- **Containers do not.** A section whose own content is only a grouping heading is a container, and its children carry the tags. This is why `## Decision Rules` in `core-codecraft.md` is untagged while `## Decision rules` in `delivery.md` is tagged — the first only groups `###` children, the second holds bullets directly. A topic heading with a `**Core bias:**` preamble *is* tagged, because that preamble is distilled content in its own right.
- **One `## Sources` block per file**, at the end, using the same `[slug]` keys the section tags use. No reference keeps per-topic sources inline.

## How to read the tags

| Tag form | Meaning |
|---|---|
| `[slug]` | The rules in that section are distilled from the named source (registry below). |
| `(synthesis)` | The section is original author synthesis — conflict arbitration, guardrails, tension lists, and verification criteria. Actionable, but not attributable to one source. |
| `(synthesis)` + `[slug]` | Synthesis that clearly extends a named source's practice; the source is listed because the framing follows it. |

Tags are **pointers, not quotations**. Every rule is a rewritten decision rule, not an
excerpt. Two consequences worth remembering:

- A tag names the *origin of the thinking*, not a page-level citation. Do not quote the skill
  as if it were the source text.
- Where a tag is `(synthesis)`, treat the rule as this skill's opinion and challenge it against
  your own incident and measurement data before applying it.

## Verification

All URLs below were probed with `curl -L` on **2026-09-24**.

| Status | Meaning |
|---|---|
| ✅ | HTTP 200 — reachable and current. |
| ⚠️ | DNS resolves but the host is **not reachable from the verification machine** (TLS/connection blocked). Almost certainly valid; verify from your own network. |
| 🔒 | Bot-gated (HTTP 403 to automated requests). The page exists; open it in a browser. |
| 📕 | Book or paid standard — no open edition to check. Cited by title and edition only. |

Re-run the probe with:

```powershell
curl.exe -s -o NUL -L --max-time 20 -w "%{http_code} %{url_effective}`n" <url>
```

## Corrections applied

Two links were dead and were replaced on 2026-09-24. Both were in the `security.md` and
`compliance-audit.md` Sources blocks:

| Old (404) | Replaced with |
|---|---|
| `https://owasp.org/www-project-application-security-verification-standard/` | `https://owasp.org/www-project-asvs/` ✅ |
| `https://csrc.nist.gov/pubs/sp/800/218/r1/upd1/final` | `https://csrc.nist.gov/pubs/sp/800/218/final` ✅ |

## Registry

### core-codecraft.md

| Slug | Source | Status |
|---|---|---|
| [aposd] | J. Ousterhout, *A Philosophy of Software Design* — https://web.stanford.edu/~ouster/cgi-bin/aposd.php | ✅ |
| [clean-code] | R. Martin, *Clean Code* | 📕 |
| [code-complete] | S. McConnell, *Code Complete*, 2nd ed. | 📕 |
| [refactoring] | M. Fowler, *Refactoring*, 2nd ed. — https://martinfowler.com/books/refactoring.html | ✅ |
| [refactoring-guru] | Refactoring.Guru — https://refactoring.guru/refactoring | ✅ |
| [pragprog] | Hunt & Thomas, *The Pragmatic Programmer*, 20th Anniversary ed. | 📕 |
| [welc] | M. Feathers, *Working Effectively with Legacy Code*; practice companion https://understandlegacycode.com/ | 📕/✅ |

### architecture-domain.md

| Slug | Source | Status |
|---|---|---|
| [clean-arch] | R. Martin, *Clean Architecture* | 📕 |
| [ddd] | E. Evans, *Domain-Driven Design* — https://www.domainlanguage.com/ddd/ | ✅ |
| [ddd-ref] | V. Vernon, *Implementing DDD* / *DDD Distilled* | 📕 |
| [poeaa] | M. Fowler, *PoEAA* — https://martinfowler.com/eaaCatalog/ | ✅ |
| [bounded-context] | M. Fowler, "BoundedContext" — https://martinfowler.com/bliki/BoundedContext.html | ✅ |
| [adr] | Architecture Decision Records — https://adr.github.io/ | ✅ |
| [c4model] | C4 model — https://c4model.com/ (supplementary) | ✅ |

### data-distributed.md

| Slug | Source | Status |
|---|---|---|
| [ddia] | M. Kleppmann, *Designing Data-Intensive Applications* — https://dataintensive.net/ | ✅ |
| [release-it] | M. Nygard, *Release It!*, 2nd ed. | 📕 |
| [jepsen] | Jepsen consistency models — https://jepsen.io/consistency | ✅ |
| [postgres-docs] | PostgreSQL 16 docs — https://www.postgresql.org/docs/16/index.html (supplementary) | ✅ |

### delivery.md

| Slug | Source | Status |
|---|---|---|
| [cd-book] | *Continuous Delivery* — https://continuousdelivery.com/ | ✅ |
| [accelerate] | *Accelerate* (Forsgren, Humble, Kim) | 📕 |
| [dora] | DORA four keys — https://dora.dev/guides/dora-metrics-four-keys/ | ⚠️ |
| [dora-reports] | DORA publications — https://dora.dev/publications/ | ⚠️ |
| [trunk-based] | https://trunkbaseddevelopment.com/ | ✅ |
| [feature-toggles] | https://martinfowler.com/articles/feature-toggles.html | ✅ |
| [sre-release-eng] | https://sre.google/sre-book/release-engineering/ | ⚠️ |
| [sre-canarying] | https://sre.google/workbook/canarying-releases/ | ⚠️ |
| [sre-postmortem] | https://sre.google/sre-book/postmortem-culture/ | ⚠️ |

### observability-sre.md

| Slug | Source | Status |
|---|---|---|
| [sre-books] | https://sre.google/books/ | ⚠️ |
| [sre-embracing-risk] | https://sre.google/sre-book/embracing-risk/ | ⚠️ |
| [sre-slo] | https://sre.google/sre-book/service-level-objectives/ | ⚠️ |
| [sre-monitoring] | https://sre.google/sre-book/monitoring-distributed-systems/ | ⚠️ |
| [sre-alerting] | https://sre.google/sre-book/practical-alerting/ | ⚠️ |
| [sre-workbook-slo] | https://sre.google/workbook/implementing-slos/ | ⚠️ |
| [sre-alerting-slos] | https://sre.google/workbook/alerting-on-slos/ | ⚠️ |
| [sre-workbook-oncall] | https://sre.google/workbook/on-call/ | ⚠️ |
| [sre-testing] | https://sre.google/sre-book/testing-reliability/ | ⚠️ |
| [obs-eng] | *Observability Engineering* — O'Reilly | 🔒 |
| [otel-signals] | https://opentelemetry.io/docs/concepts/signals/ | ✅ |
| [otel-semconv] | https://opentelemetry.io/docs/specs/semconv/ | ✅ |
| [otel-spec] | https://opentelemetry.io/docs/specs/otel/ | ✅ |
| [honeycomb-lenses] | https://www.honeycomb.io/blog/they-arent-pillars-theyre-lenses/ | ✅ |

### security.md

| Slug | Source | Status |
|---|---|---|
| [asvs] | OWASP ASVS — https://owasp.org/www-project-asvs/ | ✅ corrected |
| [owasp-cheatsheets] | https://cheatsheetseries.owasp.org/ | ✅ |
| [samm] | OWASP SAMM — https://owaspsamm.org/about/ | ✅ |
| [ssdf] | NIST SSDF SP 800-218 Rev.1 — https://csrc.nist.gov/pubs/sp/800/218/final | ✅ corrected |
| [slsa] | SLSA v1.1 — https://slsa.dev/spec/v1.1/ | ✅ |
| [owasp-top10] | https://owasp.org/www-project-top-ten/ | ✅ |
| [llm-top10] | OWASP Top 10 for LLM Applications — https://genai.owasp.org/llm-top-10/ | ✅ new |
| [genai-owasp] | https://genai.owasp.org/ | ✅ new |
| [nist-ai-rmf] | https://www.nist.gov/itl/ai-risk-management-framework | ✅ new |
| [sp-800-63b] | https://pages.nist.gov/800-63-3/sp800-63b.html | ✅ |
| [cyclonedx] / [spdx] | https://cyclonedx.org/ · https://spdx.dev/ | ✅ |
| [sigstore] | https://www.sigstore.dev/ | ✅ new |
| [scorecard] | https://securityscorecards.dev/ | ✅ |

### compliance-audit.md

| Slug | Source | Status |
|---|---|---|
| [gdpr] | https://eur-lex.europa.eu/eli/reg/2016/679/oj | ✅ |
| [gdpr-info] | https://gdpr-info.eu/ | ✅ |
| [iso-27001] | https://www.iso.org/standard/27001 | 🔒 |
| [soc2] | https://www.aicpa-cima.com/topic/audit-assurance/audit-and-assurance-greater-than-soc-2 | ✅ |
| [pci-dss] | https://www.pcisecuritystandards.org/ | ✅ |
| [eu-ai-act] | Regulation (EU) 2024/1689 — https://eur-lex.europa.eu/eli/reg/2024/1689/oj | ✅ new |
| [nist-privacy-fw] | https://www.nist.gov/privacy-framework | ✅ new |
| [nist-800-53] | https://csrc.nist.gov/pubs/sp/800/53/r5/final | ✅ new |

### infrastructure.md

| Slug | Source | Status |
|---|---|---|
| [terraform-docs] | https://developer.hashicorp.com/terraform | ✅ |
| [k8s-docs] | https://kubernetes.io/docs/ | ✅ |
| [k8s-deprecation] | https://kubernetes.io/docs/reference/using-api/deprecation-guide | ✅ |
| [argo-rollouts] | https://argo-rollouts.readthedocs.io/en/stable/ | ✅ |
| [flagger] | https://docs.flagger.app/ | ✅ |

### performance.md

| Slug | Source | Status |
|---|---|---|
| [systems-performance] | B. Gregg, *Systems Performance*, 2nd ed. | 📕 |
| [use-method] | https://www.brendangregg.com/usemethod.html | ✅ |
| [linuxperf] | https://www.brendangregg.com/linuxperf.html | ✅ |
| [hp-mysql] | *High Performance MySQL* (Schwartz et al.) | 📕 |
| [mysql-manual] | https://dev.mysql.com/doc/refman/8.4/en/performance-schema.html | ✅ |

### incident-chaos.md

| Slug | Source | Status |
|---|---|---|
| [principles-of-chaos] | https://principlesofchaos.org/ | ✅ |
| [chaos-eng] | *Chaos Engineering* (O'Reilly) — https://www.oreilly.com/library/view/chaos-engineering/9781492043850/ | 🔒 |
| [sre-managing-incidents] | https://sre.google/sre-book/managing-incidents/ | ⚠️ |
| [pagerduty-ir] | https://response.pagerduty.com/ | ✅ |
| [sre-postmortem] | https://sre.google/sre-book/postmortem-culture/ | ⚠️ |
| [atlassian-postmortem] | https://www.atlassian.com/incident-management/postmortem/blameless | ✅ |
| [sre-slo] | https://sre.google/sre-book/service-level-objectives/ | ⚠️ |
| [sre-alerting-slos] | https://sre.google/workbook/alerting-on-slos/ | ⚠️ |

### team-process.md

| Slug | Source | Status |
|---|---|---|
| [team-topologies] | *Team Topologies* (Skelton & Pais) — https://teamtopologies.com/key-concepts | ✅ |
| [adr] | Architecture Decision Records — https://adr.github.io/ | ✅ |
| [cognitect-adr] | M. Nygard, "Documenting Architecture Decisions" — https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions | ✅ |
| [fowler-debt-quadrant] | https://martinfowler.com/bliki/TechnicalDebtQuadrant.html | ✅ |
| [fowler-debt] | https://martinfowler.com/bliki/TechnicalDebt.html | ✅ |

## Per-section mapping

Sections tagged `(synthesis)` are omitted here — they have no upstream source by design.

| File | Section | Slugs |
|---|---|---|
| core-codecraft.md | Design interfaces and modules | [aposd] |
| core-codecraft.md | Write functions | [clean-code] [code-complete] |
| core-codecraft.md | Stay pragmatic | [pragprog] |
| core-codecraft.md | Refactor safely | [refactoring] [refactoring-guru] |
| core-codecraft.md | Change legacy code | [welc] |
| architecture-domain.md | Set dependency direction | [clean-arch] [adr] |
| architecture-domain.md | Model the domain | [ddd] [ddd-ref] [bounded-context] |
| architecture-domain.md | Apply enterprise patterns | [poeaa] |
| data-distributed.md | Set ground truths | [ddia] |
| data-distributed.md | Model data and derived data | [ddia] |
| data-distributed.md | Replicate, partition, transact | [ddia] [jepsen] |
| data-distributed.md | Harden the runtime | [release-it] |
| delivery.md | Core model | [cd-book] [accelerate] [dora] [trunk-based] |
| delivery.md | Decision rules | [cd-book] [accelerate] [feature-toggles] |
| delivery.md | Evidence / verification | [dora] [dora-reports] |
| observability-sre.md | Mental model | [sre-embracing-risk] [obs-eng] [honeycomb-lenses] |
| observability-sre.md | SLO / error budget decision rules | [sre-slo] [sre-workbook-slo] [sre-alerting-slos] |
| observability-sre.md | Alerting decision rules | [sre-alerting] [sre-monitoring] [sre-workbook-oncall] |
| observability-sre.md | Evidence / verification | [sre-testing] [sre-postmortem] |
| security.md | Threat modeling | [asvs] [ssdf] [samm] [owasp-cheatsheets] |
| security.md | Authentication & authorization | [asvs] [owasp-cheatsheets] [sp-800-63b] |
| security.md | Secrets management | [owasp-cheatsheets] [ssdf] |
| security.md | Dependencies & software composition | [asvs] [ssdf] [cyclonedx] [spdx] [scorecard] |
| security.md | Supply chain & build integrity | [slsa] [ssdf] [sigstore] |
| security.md | Data protection | [owasp-cheatsheets] |
| security.md | Secure code review & testing | [ssdf] [asvs] [owasp-top10] |
| security.md | Incident response handoff | [ssdf] |
| compliance-audit.md | Read this first | [ssdf] [asvs] [samm] [slsa] |
| compliance-audit.md | GDPR | [gdpr] [gdpr-info] |
| compliance-audit.md | Framework caveats | [iso-27001] [soc2] [pci-dss] |
| compliance-audit.md | Audit evidence | [ssdf] [asvs] [samm] [slsa] |
| compliance-audit.md | Change control | [ssdf] [samm] [slsa] |
| compliance-audit.md | Readiness practice | [ssdf] [asvs] |
| infrastructure.md | IaC: state and backends | [terraform-docs] |
| infrastructure.md | IaC: drift and immutability | [terraform-docs] |
| infrastructure.md | Kubernetes: resources | [k8s-docs] |
| infrastructure.md | Kubernetes: probes | [k8s-docs] |
| infrastructure.md | Kubernetes: rollouts | [k8s-docs] [argo-rollouts] [flagger] |
| infrastructure.md | Version-sensitive caveats | [k8s-deprecation] [terraform-docs] |
| performance.md | Workload models | [systems-performance] [use-method] |
| performance.md | Percentiles and latency | [systems-performance] |
| performance.md | Benchmarks | [systems-performance] |
| performance.md | Capacity | [systems-performance] |
| performance.md | Caching | [systems-performance] [hp-mysql] |
| performance.md | Database diagnosis (MySQL) | [hp-mysql] [mysql-manual] |
| performance.md | Metrics to collect | [systems-performance] [mysql-manual] |
| incident-chaos.md | 1. Chaos Engineering — Principles / Decision rules / Ownership / Stop conditions / Anti-patterns / Evidence | [principles-of-chaos] [chaos-eng] |
| incident-chaos.md | 2. Incident Response — Decision rules / Ownership / Stop conditions / Anti-patterns / Evidence | [sre-managing-incidents] [pagerduty-ir] |
| incident-chaos.md | 3. Blameless Postmortems — Decision rules / Ownership / Stop conditions / Anti-patterns / Evidence | [sre-postmortem] [atlassian-postmortem] |
| incident-chaos.md | 4. SLO Enforcement in an Incident Context — Decision rules / Ownership / Stop conditions / Anti-patterns / Evidence | [sre-slo] [sre-alerting-slos] |
| team-process.md | 1. Team Topologies — Team types / Decision rules / Ownership / Stop conditions and anti-patterns / Evidence | [team-topologies] |
| team-process.md | 2. ADR Practice — Decision rules / Ownership / Stop conditions / Anti-patterns / Evidence | [adr] [cognitect-adr] |
| team-process.md | 3. Technical Debt Management — Decision rules / Ownership / Stop conditions / Anti-patterns / Evidence | [fowler-debt-quadrant] [fowler-debt] |

## Known gaps

- **`SKILL.md` has no per-rule provenance.** The risk tiers (T1–T4), the six-rule conflict
  order, the nine-step workflow, and the evidence rules are this skill's own orchestration
  design. They are `(synthesis)` by nature; treat them as a convention to adopt or change,
  not as findings from the sources above.
- **Checklist provenance is transitive, not direct.** The four `checklists/` files now state
  in a `**Sources:**` line which references they draw on, but their items are not keyed to
  slugs. To reach a primary source from a checklist item, follow checklist → reference →
  slug → this ledger. Do not cite a checklist item as coming from OWASP or NIST.
- **Subsection tags within a topic are not differentiated.** In `incident-chaos.md` and
  `team-process.md`, all six (or five) subsections of a topic share the same slugs, because
  the source material states those rules at topic level. A per-subsection split would be an
  invention rather than a distillation.
- **Unverified links.** Everything marked ⚠️ or 🔒 could not be confirmed from the
  verification machine. Re-check them before quoting this skill in an audit or PR.
- **Not included deliberately:** tool-specific vendor blogs, LLM prompt-engineering content,
  and benchmarks from vendor marketing — none meet the bar for a decision rule here.
