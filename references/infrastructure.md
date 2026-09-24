# Infrastructure as Code & Kubernetes — State, Workloads, Rollouts

> Actionable IaC (Terraform) and Kubernetes guidance distilled from official docs into original decision rules. Terraform and Kubernetes evolve fast: verify API versions and behaviors against current official docs (Sources) before acting.

## IaC: state and backends
**Sources:** [terraform-docs]

- State maps config → real resources and drives planning; keep it in a remote backend with locking (S3/GCS/Azure + lock table, or Terraform Cloud) — never a local file for shared environments.
- Never edit state by hand; use `terraform state mv/rm/import` for surgery. State holds secrets: encrypt at rest and restrict access.
- Pin provider versions (constraints + lock file) and mind CLI-vs-state-format compatibility; read upgrade notes — a newer CLI may migrate state formats.
- Structure by environment and component (workspaces or per-env directories); share logic via modules with pinned versions, and review `terraform plan` output in code review like any diff.

## IaC: drift and immutability
**Sources:** [terraform-docs]

- Drift = real world diverged from config/state (console edits, out-of-band changes, deleted resources). Treat it as an incident: find the root cause, do not just re-apply.
- Detect drift with scheduled `terraform plan` in CI, or `plan/apply -refresh-only` (Terraform ≥ 1.1) to report differences without mutating; import pre-existing resources with `terraform import` instead of fighting them.
- Prefer immutable, replace-over-mutate: build new images (AMI/container) and recreate rather than SSH-and-patch; environments stay reproducible and rollback stays simple.
- Know lifecycle meta-arguments: `create_before_destroy` for zero-downtime replacement, `prevent_destroy` for protected resources; `ignore_changes` only with a written reason.
- Enforce policy as code (Sentinel/OPA) for guardrails such as tags, regions, and forbidden resource types — drift detection alone cannot stop bad applies.

## Kubernetes: resources
**Sources:** [k8s-docs]

- Set requests per container — they drive scheduling. Set limits deliberately: a memory limit caps usage (excess = OOM kill), a CPU limit caps (excess = throttling); do not set limits below observed steady-state use.
- Size requests from observed steady-state usage with headroom; a memory limit below steady use OOM-restarts pods; a CPU limit throttles latency-sensitive work — measure before applying limits blindly.
- Namespace-level ResourceQuotas and LimitRanges bound runaway usage; scale with the HPA (autoscaling/v2) on a real metric, bounded by min/max.
- Pin images by digest (or fixed tag, never `latest`); every deploy must name a rollback target.
- Use PodDisruptionBudgets to bound availability loss during voluntary disruptions (node drains, rollouts): size minAvailable/maxUnavailable so a drain leaves enough replicas to meet availability, and note that PDBs do not cover involuntary disruptions (node crashes) — replication does. A PDB that can never be satisfied blocks drains entirely; pair with topology spread for multi-zone resilience.

## Kubernetes: probes
**Sources:** [k8s-docs]

- Three probes, three jobs: liveness restarts deadlocked containers; readiness admits traffic only when able to serve; startup gates liveness during slow boot so a cold app is not killed early — add a startup probe only when boot is genuinely slow; fast-booting apps do not need it.
- Tune initialDelaySeconds/periodSeconds/timeoutSeconds/failureThreshold deliberately — a flapping probe removes or restarts healthy pods and looks like random errors. failureThreshold × periodSeconds bounds how long a pod is marked unhealthy; tune it to tolerate brief hiccups without masking real failures.
- Failure semantics: readiness failure = removed from Service endpoints (no traffic, no restart); liveness failure = container restart. Do not key readiness off flaky external services or traffic-only work.
- Expose a dedicated, cheap health endpoint and prefer httpGet with an explicit port over exec shells in probes.

## Kubernetes: rollouts
**Sources:** [k8s-docs] [argo-rollouts] [flagger]

- Prefer RollingUpdate with deliberate maxUnavailable/maxSurge chosen from your capacity and availability budget: 0/1 keeps capacity constant but needs headroom for the surge pod; 1/0 avoids the surge pod but accepts a one-replica dip (values may be integers or percentages); the rollout advances only as new pods become ready — gate it on readiness, optionally with minReadySeconds.
- Use Recreate only where downtime is acceptable (stateful batch, migrations). Never edit live pods in place — change the manifest and let the controller converge.
- Verify with `kubectl rollout status deployment/<name>` and `kubectl rollout history`; roll back with `kubectl rollout undo`; keep revisionHistoryLimit so undo has a target.
- For canary/progressive delivery use Argo Rollouts/Flagger or two Deployments with traffic split — promote only while metrics stay green, and let the tool auto-abort on failure.
- For stateful workloads use StatefulSets with ordered rollout and per-pod identity; budget the downtime cost of ordered updates explicitly.

| Strategy | Use when | Caveat |
|---|---|---|
| Recreate | downtime OK; stateful batch, migrations | all old pods terminate before new ones start |
| RollingUpdate | stateless services, default | slow if maxSurge is large; gate on readiness |
| Blue/green | instant rollback needed | double capacity during cutover |
| Canary | progressive, metric-gated delivery | needs traffic-split tooling |

## Version-sensitive caveats
**Sources:** [k8s-deprecation] [terraform-docs]

- Kubernetes deletes deprecated APIs: extensions/v1beta1 and apps/v1beta* are gone; Ingress is networking.k8s.io/v1. Check `kubectl api-versions` and the official API deprecation guide — a manifest that validated last year may fail apply today.
- Feature maturity shifts schemas: startupProbe (GA ~1.20), autoscaling/v2 (GA ~1.23). Never assume a beta/alpha shape is stable; test on the cluster version you deploy to.
- Terraform: `-refresh-only` needs ≥ 1.1; state format and provider SDKs evolve — validate against a sandbox with the exact pinned versions before touching production; major provider upgrades may re-create resources, plan for it.
- kubectl skew policy: your client must stay within one minor version of the server — mismatched versions fail or misbehave silently.

## Verification
**Sources:** (synthesis)

- CI gate: `terraform fmt` + `terraform plan` (the real check — it talks to providers and state; `validate` is only static) with fail-on-diff for protected dirs, plus `kubectl apply --dry-run=server -o yaml` — server-side validation exercises the cluster's schema and admission path, which client-side dry-run cannot — and `kubectl diff` in review.
- Post-apply: rollout status, pod events (`kubectl get events --sort-by=.lastTimestamp`), probe failures (`kubectl describe pod`), and a clean drift scan at the next plan cycle.

## Sources

- [terraform-docs] Terraform: https://developer.hashicorp.com/terraform (state, backends, lifecycle, import, `-refresh-only`).
- [k8s-docs] Kubernetes: https://kubernetes.io/docs/ (pods/probes, deployments, resources, HPA).
- [k8s-deprecation] Kubernetes API deprecation guide: https://kubernetes.io/docs/reference/using-api/deprecation-guide
- [argo-rollouts] Argo Rollouts: https://argo-rollouts.readthedocs.io/en/stable/
- [flagger] Flagger: https://docs.flagger.app/

Slug definitions, verification status, and per-section mapping: `references/source-map.md`.
