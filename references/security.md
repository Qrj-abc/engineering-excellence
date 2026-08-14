# Security Engineering Reference

Guidance for building, reviewing, and hardening software produced by this skill.
It is engineering practice, not a compliance claim — see `references/compliance-audit.md`.
Framework tags: [ASVS] OWASP ASVS (4.0.3 at time of writing — check for the current release) · [SSDF] NIST SP 800-218 Rev.1 · [SAMM] OWASP SAMM 2.0 · [SLSA] SLSA v1.0.

## Threat modeling
- Model before building: per feature, list assets (code, secrets, data, pipelines), trust boundaries, entry points, and actors; then apply STRIDE and keep only threats realistic for your deployment.
- [ASVS V1, SSDF PW.1, SAMM TA] Write the model into the design doc: assumptions, mitigations, accepted risks. Revisit it on any architectural or major dependency change, not just at release.
- Match modeling depth to risk: a full data-flow diagram for high-risk or complex changes; a lightweight pass (entry points, trust boundaries, controls) for small or low-risk changes; skip ceremony where nothing crosses a trust boundary. The durable decisions matter more than the tool (OWASP Threat Dragon or a whiteboard both work).
- Keep the diagram in the repo next to the design doc; track residual/known risks as issues with an owner and a due date.
- Ask per boundary: what can go wrong here, who can reach this entry point, and what happens when a control fails.

## Authentication & authorization
- [ASVS V2/V3] Use a maintained identity provider or a well-reviewed library; never hand-roll password hashing, session tokens, or MFA.
- Passwords: store only salted slow hashes (Argon2id, scrypt, bcrypt, PBKDF2) — see OWASP Password Storage Cheat Sheet. Require MFA by risk tier: accounts that can reach production, customer data, or deploy pipelines (admin consoles, remote access, CI/CD credentials) warrant it; scale enforcement to risk rather than applying it uniformly.
- Sessions: choose the mechanism that fits the client — browser cookies (HttpOnly, Secure, SameSite, regenerate ID on login) for web, opaque server-side session tokens for APIs/CLIs; short lifetimes, rotate on privilege change, invalidate on logout and on compromise.
- [ASVS V4] Authorize at every API and function, server-side, default-deny; never trust client-supplied roles. Centralize access-control logic in one auditable policy module.
- Tokens: short-lived OAuth/OIDC access tokens verified for issuer and audience where an identity provider is in play; opaque random tokens with server-side lookup are fine for internal or non-web clients; never log tokens; CSRF tokens on state-changing browser requests; rate-limit auth endpoints without enabling lockout-based DoS.
- [SSDF PO.3] Secure environments with least privilege for human and machine identities; separate human vs. service accounts.

## Secrets management
- Never commit secrets: scan repos and CI logs (gitleaks, trufflehog, or platform secret scanning) on every push and block findings in CI.
- Store in a secret manager — vault, KMS, or the platform's native store (cloud secret manager, CI/CD encrypted variables, OS keychain) — and inject at runtime (env or mounted secrets); never bake secrets into images or source.
- Automate rotation with short-lived credentials (OIDC federation for cloud, short-lived registry tokens); document owner, rotation cadence, and rollback path for every secret. Where policy requires it — or the secret is shared across teams — notify the security team before rotating.
- [SSDF PS.1] Treat secrets as code to protect: encrypt at rest and in transit; keep an inventory of what exists, who can read it, and when it rotates.
- Test fixtures and local dev use clearly fake values or a dev store — never real credentials; keep secret references (KMS ARNs, secret paths, CI variable names) centralized rather than duplicated.

## Dependencies & software composition
- [ASVS V14.2, SSDF PO.2] Maintain an SBOM (CycloneDX or SPDX) of direct and transitive dependencies where distribution, compliance, or risk warrants it (published artifacts, regulated data, high-value services); regenerate it each release.
- Scan in CI with an SCA tool (OWASP Dependency-Check, Dependabot, or commercial); set fix SLAs by severity and fail builds on critical/high findings — adjusted by exploitability and exposure (reachable, internet-facing, attackable path) rather than severity alone, with documented, owned exceptions.
- Lockfiles pin the exact resolved versions and verify checksums/signatures; manifests may declare reviewed ranges, but ban `latest`/floating tags in production. Vet new packages for typosquatting (name, author, age, downloads) before adopting.
- Automate upgrades (Renovate/Dependabot PRs) with a review SLA; run license checks alongside security scans.

## Supply chain & build integrity
- [SLSA] Choose an SLSA level by artifact risk and distribution: public-facing binaries and high-value production artifacts justify SLSA 2–3 (controlled CI/CD platform, hermetic and reproducible builds, signed provenance); internal tools can stop at provenance records (SLSA 1) — see below.
- [SSDF PS.3] Harden the pipeline: least-privilege service accounts, protected branches with required reviews, no secrets in build logs; sign commits where author impersonation or attestation is in the threat model (branch protection plus review may suffice elsewhere); pin CI base images by digest and pin third-party actions to an immutable digest or commit SHA (reviewed once, then locked) rather than mutable tags.
- [SSDF PS.2] Sign artifacts where distribution, compliance, or consumer verification justifies it (cosign for images, release signing) and verify signatures at deployment time; internal hash-verified pipelines may skip it.
- If full hardening is too heavy for internal tools, still record provenance (SLSA 1) and sign it when feasible rather than shipping unattested.
- Publish the per-release SBOM plus a vulnerability-triage runbook: who fixes, SLA, and how disclosures reach users.

## Data protection
- Classify data (public / internal / confidential / regulated) and record the class next to the data model; apply controls by class, not by guess.
- Encrypt in transit with TLS — minimum TLS 1.2, prefer TLS 1.3 where supported, and disable weak protocol/cipher configurations (for example TLS 1.0/1.1, RC4, 3DES, NULL/export ciphers). Apply HSTS for web endpoints where the deployment controls the full HTTP response chain; do not treat HSTS as a blanket requirement for non-web transports. Encrypt sensitive data at rest with managed keys and rotation — see OWASP Cryptographic Storage and Transport Layer Protection cheat sheets.
- Minimize: collect only what the feature needs, define retention and deletion schedules, and implement purge. EU personal data: see GDPR notes in `references/compliance-audit.md`.
- Back up encrypted and test restores on a schedule; define RTO/RPO per data class. Prefer pseudonymization for analytics.
- Log security-relevant events (failed logins, denied access, admin actions, secret access) without logging secrets or personal data; centralize and protect logs from tampering.

## Secure code review & testing
- Run SAST in CI (Semgrep, CodeQL, or platform equivalent) where code risk warrants it; block critical/high findings on merge unless a documented, owned exception applies. [SSDF PW.7]
- Human review checklist: injection (SQL/command/template), XSS, SSRF, insecure deserialization, IDOR, missing authz checks, unsafe defaults. Map to ASVS L1 / OWASP Top 10.
- Include negative tests: unauthorized access attempts, boundary inputs, tampered tokens/roles. [SSDF PW.8]
- [SSDF RV.2] Before release, confirm known vulnerabilities are fixed or formally accepted with an owner and date.

## Incident response handoff
- Define an escalation path and runbook before an incident; preserve evidence (logs, snapshots, chat) when it is safe and fast to do so — never let evidence capture delay containment.
- Practice a tabletop drill on a cadence that matches how often your threat model and systems change — annual is a common baseline for stable services; run one sooner after major architectural or threat changes. After an incident, update the threat model and regression tests. [SSDF RV.4]

## Sources
- OWASP ASVS: https://owasp.org/www-project-application-security-verification-standard/
- OWASP Cheat Sheet Series: https://cheatsheetseries.owasp.org/ (Authentication, Authorization, Password Storage, Secrets Management, Cryptographic Storage, Transport Layer Protection, Threat Modeling, Dependency Management)
- OWASP SAMM: https://owaspsamm.org/
- NIST SSDF SP 800-218 Rev.1: https://csrc.nist.gov/pubs/sp/800/218/r1/upd1/final
- SLSA v1.0: https://slsa.dev/spec/v1.0/
- OWASP Top 10: https://owasp.org/www-project-top-ten/
- NIST SP 800-63B (identity guidelines): https://pages.nist.gov/800-63-3/sp800-63b.html
- SBOM formats: https://cyclonedx.org/ · https://spdx.dev/
- OpenSSF Scorecard: https://securityscorecards.dev/
