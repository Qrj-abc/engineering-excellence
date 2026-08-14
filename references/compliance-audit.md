# Compliance & Audit Reference

## Read this first (honest framing)
- This file makes engineering work audit-ready; it is not compliance. Only an accredited certification body (ISO/IEC 27001), a licensed CPA firm (SOC 2), a Qualified Security Assessor (PCI DSS), or a supervisory authority's assessment (GDPR) can attest compliance.
- Never write "compliant" or "certified" because of this skill. State facts instead: "practices follow SSDF/ASVS", "controls mapped to X", "certification audit scheduled for Q3".
- Core practice frameworks are SSDF, ASVS, SAMM, SLSA. ISO/SOC/PCI references below are scoping context with explicit caveats.

## GDPR — applies when you process EU/EEA personal data
- Principles (Art. 5): lawfulness, purpose limitation, data minimization, accuracy, storage limitation, integrity & confidentiality, accountability. Design against these, not just for them.
- Records of processing (Art. 30): document data categories, purposes, recipients, retention, and international transfers — the first thing an authority asks for.
- Security of processing (Art. 32): risk-appropriate technical and organizational measures. This is where engineering controls (encryption, access control, testing, resilience, incident response) map in; record the risk assessment behind your choices.
- Breach notification (Art. 33/34): report to the supervisory authority within 72 hours when feasible, notify data subjects where risk is high, and keep an internal breach log regardless.
- Rights (Art. 15–22) and DPIA (Art. 35): support export, rectification, erasure, and portability in data models; run a DPIA for high-risk processing. Privacy by design/default (Art. 25) belongs in the design review (see `references/security.md` threat modeling).
- Processors & transfers (Art. 28, 46): sign DPAs with processors, keep a subprocessor list, and add transfer safeguards for data leaving the EEA.
- Subject requests (Art. 12): respond within one month; make export/erasure paths part of the data model, not one-off scripts.
- Official text: https://eur-lex.europa.eu/eli/reg/2016/679/oj

## Framework caveats — do not self-certify
- ISO/IEC 27001:2022 — management-system certification issued by an accredited certification body after formal audit. It certifies your ISMS, not your product. Useful as a control structure (Annex A), not as a self-assessment.
- SOC 2 (AICPA) — an attestation for service organizations issued by a CPA firm against the Trust Services Criteria; Type I reviews design, Type II adds operating effectiveness over time. Relevant only if you are a service organization whose customers rely on your controls.
- PCI DSS v4.0 — applies only where cardholder data is stored, processed, or transmitted (the CDE); assessed by a QSA or via SAQ. Irrelevant as a general security baseline for non-payment software.

## Audit evidence
- Evidence must be specific, contemporaneous, attributable, and preserved; a control without evidence did not happen in an auditor's eyes.
- Keep per release: threat model and design decisions, scan and test reports (dated, with versions), SBOMs, SCA results, review/approval records, pipeline configs, secret-rotation logs, incident log, training records.
- Name evidence consistently (e.g., YYYY-MM-release-control) so an auditor can reconstruct a release from records alone.
- Protect logs: append-only and tamper-evident, centralized, NTP-synced clocks, retention aligned to contract/regulation, and free of secrets and unnecessary personal data.
- Attribution: every action (commit, review, deploy, admin access) traceable to a human or machine identity.

## Change control
- Define change controls by risk: for production and regulated data, separate authoring from reviewing/deploying (author ≠ reviewer ≠ deployer) and forbid direct pushes; where full separation is impractical (small teams), compensate with stronger automated gates, post-change review, and documented exceptions — scale the gate to risk instead of applying one shape everywhere.
- Gate promotions in CI/CD: static analysis, SCA, secret scan, tests, and for production also SBOM generation, provenance, and signing.
- Document every change: what, why, who approved, rollback plan; keep records queryable for the whole audit window.
- Emergency changes may take a fast path but must be documented identically afterwards, with a post-change review ([SAMM SD]).
- Record the commit → build → artifact chain for each release — this doubles as SLSA provenance and answers "what exactly did we ship?".
- Configuration as code with drift detection; define an approval matrix (who may merge/promote by risk level).

## Common audit questions
- What data do we store, where, and why? → point to records of processing and the data model.
- Who can access production, and how is that proven? → access reviews, identity logs, least-privilege evidence.
- How is a change promoted, and who approved it? → change records, review/approval trails, pipeline logs.
- What was the last incident, and how was it handled? → incident log, timeline, notification decisions.

## Readiness practice (not certification)
- Map your practices to SSDF practice groups (PO/PS/PW/RV) and ASVS chapters so due diligence is visible and reviewable.
- Maintain a control-to-evidence matrix, updated each release, so an audit is answered from records instead of firefighting.
- Before an audit: assemble the evidence package, walk each control with its owner, and fix discovered gaps first.

## Sources
- GDPR (EUR-Lex): https://eur-lex.europa.eu/eli/reg/2016/679/oj · readable copy: https://gdpr-info.eu/
- ISO/IEC 27001:2022: https://www.iso.org/standard/27001 (standard text is paid; certification runs through accredited bodies)
- SOC 2 (AICPA): https://www.aicpa-cima.com/topic/audit-assurance/audit-and-assurance-greater-than-soc-2
- PCI DSS: https://www.pcisecuritystandards.org/
- NIST SSDF: https://csrc.nist.gov/pubs/sp/800/218/r1/upd1/final
- OWASP ASVS: https://owasp.org/www-project-application-security-verification-standard/
- OWASP SAMM: https://owaspsamm.org/
- SLSA: https://slsa.dev/spec/v1.0/
