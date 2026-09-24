# Data & Distributed Systems — Consistency, Reliability, Replay

Scope: designing data models, storage, replication, partitioning, streams, or anything with a network between writers and readers. Sources: Designing Data-Intensive Applications (Kleppmann); Release It! (Nygard) reliability patterns where data/distributed concerns apply.

## Decision Rules

### Set ground truths
**Sources:** [ddia]
- Do not design as if every write, read, replica, and queue were local, ordered, fresh, and exactly-once.
- Treat crashes, partial writes, duplicates, timeouts, stale reads, and unknown downstream success as normal inputs.
- Name the source of truth and the consistency expectation before choosing a topology or model.
- Describe load with concrete numbers — request rates, volume, access patterns, latency percentiles — before changing architecture.
- State the fault model: network delay, partitions, duplicate delivery, pauses, stale leaders, wall-clock uncertainty.
- Latency is a distribution; track percentiles (p50/p99/p999), never just averages.

### Model data and derived data
**Sources:** [ddia]
- Choose data models from relationships, access patterns, consistency needs, update locality, and evolution pressure.
- Indexes, caches, search copies, read models, materialized views, and denormalized fields are derived data: give each an explicit propagation path, lag bound, observability, and rebuild path.
- Commands, jobs, events, and stream processors must be safe under retry and replay: dedup keys, idempotent transitions, or explicit recovery contracts.
- Preserve only the ordering business logic actually needs; scope it per key, stream, or partition.
- Separate commands, events, durable logs, streams, and materialized views; events describe facts, and consumers tolerate lag, duplicates, restart, and versioned payloads.
- Treat schemas, APIs, messages, and events as evolving contracts across old and new readers, writers, and stored data.
- Use event sourcing only when the event sequence is the right persistence model, not as a default.
- Publish changes through an outbox or CDC when a transaction must also notify other systems — dual writes are a trap.
- Denormalize only where read patterns justify it; keep the write path the single source of truth.

### Replicate, partition, transact
**Sources:** [ddia] [jepsen]
- Choose replication from write topology, latency, failure tolerance, lag, failover, and conflict handling — not from fashion.
- Partition by workload-relevant locality; be explicit about hot keys, skew, routing, and rebalancing cost.
- Match transactions and isolation to named invariants; scope atomicity, commit behavior, recovery, and reconciliation explicitly.
- Use linearizability, total order broadcast, atomic commit, or consensus only where the coordination problem truly requires agreement.
- Make batch and stream processing recomputable and recoverable: define inputs, outputs, checkpoints, event time vs. processing time, windows, late data, and joins.
- Separate command and query models (CQRS) when read and write shapes diverge meaningfully.
- Sync replication buys stronger durability, async buys lower latency — make the trade explicit per data class.

### Harden the runtime (Release It!, distributed)
**Sources:** [release-it]
- Assume every dependency can fail slow, partial, or prolonged; fail visibly, limit blast radius, shed load, and preserve core service.
- Put explicit deadlines on every outbound call; no infinite waits.
- Retry only when safe: bounded count and total time, backoff with jitter, never on validation errors.
- Isolate failures with circuit breakers, fast failure, bulkheads, and separate resource pools.
- Design overload explicitly: back pressure, finite queues, demand limits, load shedding.
- Budget scarce resources explicitly; release deterministically; never hold locks across slow calls.
- Build observability into boundaries: correlation IDs, latency, throughput, error, saturation, queue depth, retry and breaker state, health signals.
- Make startup, health checks, migrations, deployments, and controls idempotent, restartable, observable, stoppable.
- Route transient failures to bounded retry paths and fail fast on permanent ones.

## Applicability Guardrails
**Sources:** (synthesis)
- A single ACID store beats five eventually-consistent caches until measurement says otherwise; do not build a distributed fabric by default.
- Exactly-once is real but conditional: it needs coordinated machinery (transactional state plus idempotent effects, or stream-processor transactions) and costs throughput, latency, and complexity. Default to at-least-once plus idempotency; adopt exactly-once only where duplicates are genuinely unacceptable and the coordination cost is justified.
- Consensus and linearizability where a single-node answer suffices are self-inflicted cost.
- Read scaling is usually the only real need; choose replication topology by the write path, not by novelty.
- Reliability patterns apply where partial failure is possible: a single process needs timeouts, but not a circuit breaker.
- Do not add ordering, transactions, or consensus beyond what a named business invariant requires.
- Apply Release It! hardening at every dependency boundary — a remote call without a timeout is a fault waiting to happen.
- Adopt new storage or coordination technology only after measuring a real bottleneck it removes.

## Common Tensions
**Sources:** (synthesis)
- Consistency vs. availability and latency — decide per operation, not globally; document staleness bounds.
- Normalized truth vs. read performance — derived data with an explicit rebuild path beats hidden denormalization.
- Strong ordering vs. partition scalability — scope ordering to the key that needs it.
- Durable vs. fast writes — durability is a point-in-time contract; state it instead of hand-waving fsync.
- Replay safety vs. simplicity — dedup keys and idempotency are cheap insurance on every message boundary.
- Isolation vs. resource utilization — bulkheads trade capacity for survivability; size pools by demand, not habit.
- Eventual consistency vs. user expectations — surface staleness in the UI instead of pretending freshness.
- Strong durability vs. write latency — configure durability per write class (acks, fsync policy) instead of one global setting.

## Verification
**Sources:** (synthesis)
- For every read and write path, can you state: source of truth, staleness bound, retry semantics, and duplicate handling?
- Fault drill: kill a replica, pause a dependency, duplicate a message, replay a job — record what happens and fix surprises.
- Is every processor idempotent, and is every retry bounded and safe under duplicate delivery?
- Can all derived data be rebuilt from the log or source without downtime?
- Are timeouts, queues, pools, caches, and payloads bounded and observable?
- Do percentiles (not averages) meet the stated SLO under the described load?
- After any schema or event change, mixed-version readers and writers keep working or fail loudly.
- Exercise recovery paths (failover, replay, rebuild) before a real incident for any distributed feature; run full chaos experiments where surprise failure is expensive and the environment allows it — not as a blanket requirement.

## Sources

- [ddia] M. Kleppmann, *Designing Data-Intensive Applications* — https://dataintensive.net/
- [release-it] M. Nygard, *Release It!*, 2nd ed. (book; no open edition)
- [jepsen] Jepsen — consistency models map — https://jepsen.io/consistency
- [postgres-docs] PostgreSQL 16 documentation (transaction isolation, replication) — https://www.postgresql.org/docs/16/index.html (supplementary; not the source of any rule here)

Slug definitions, verification status, and per-section mapping: `references/source-map.md`.
