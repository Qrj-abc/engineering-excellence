# Performance Engineering — Measurement, Capacity, and Tuning

> Actionable performance guidance distilled from *Systems Performance* (Gregg) and *High Performance MySQL* (Schwartz et al.) into original decision rules — no book excerpts. Confirm claims against your product version and workload before acting.

## Workload models

- Describe load before tuning: request rate, in-flight concurrency, payload size, read/write mix, data volume, hot-set size, arrival pattern (steady, bursty, periodic), and think time. A tuning result is only valid for the workload it was measured under.
- Four workload pillars: demand, access pattern, locality (hot data), and skew (few keys dominate). Averages hide all four — always measure distributions.
- USE method per resource (CPU, memory, disk, network, locks): Utilization = busy/total time, Saturation = queued work, Errors. High utilization alone is not a problem; sustained saturation with growing queues is the signal to act.
- Little's Law in operation: concurrency ≈ throughput × latency. If latency rises, throughput must fall or concurrency must rise — use it to sanity-check why a queue is growing.
- Separate on-CPU from off-CPU time (I/O, locks, network, scheduler waits). A "slow query" may be waiting, not computing — profile both before choosing a fix.

## Percentiles and latency

- Track p50/p90/p95/p99 (plus p999 on critical paths) per tier; state SLAs as percentiles ("p99 < 200 ms"), never as averages.
- Fan-out multiplies tails: a request awaiting N parallel dependencies experiences roughly the max, not the mean, of their latencies — budget per-leg percentiles so the aggregate meets the SLA.
- High percentiles need enough samples; a short or small sample makes p99 noise. Percentiles cannot be averaged across time buckets — monitor them per window.
- Bimodal latency means a discrete cause (GC pause, cold cache, connection setup, lock contention). Investigate the modes, not the mean.
- Tail causes to hunt: slowest replica/shard, unbalanced keys, garbage collection, connection churn, network retries. The tail is often dominated by one mechanism, but may have several — measure and attribute before fixing.

## Benchmarks

- Benchmark your workload, not the tool: real queries/endpoints, real data shape and volume, realistic concurrency. Microbenchmarks isolate one mechanism only — label them as such.
- Control the environment: dedicated machines, same CPU governor, no noisy neighbors, fixed dataset, warmed caches, defined steady-state window (ignore warm-up and run tail).
- Report the distribution (p50/p99, error rate, throughput) plus environment and versions — a bare average is not reproducible evidence.
- Add regression gates where performance is a claim or risk: rerun the relevant benchmark before/after the change; fail review if p99/throughput move beyond a written tolerance (e.g., p99 +5%). Scope the gate to the touched path — not every change needs the full suite.
- Load tests target a defined utilization or concurrency level derived from your capacity plan and SLO — e.g., the point where p99 latency starts to degrade for latency-sensitive services — not raw max throughput; add soak (hours at sustained load) and spike (sudden burst) variants.
- Benchmark only what the change touches, then re-run the full suite: partial evidence beats none, full evidence beats partial.

## Capacity

- Model demand = current peak × growth × peak factor; plan for the peak percentile, not the mean. Write down assumptions (growth rate, retention, hot-set growth) so the model is auditable.
- Size for the failures you accept: if you run N replicas with a defined failover window, losing one must still meet SLA within that window — N+1 is a common starting shape, not a universal requirement. Set utilization ceilings where queues start growing (measured saturation), not at a fixed percentage; keep memory below swap.
- Plan DB capacity explicitly: storage growth and retention, connection pool vs backend concurrency, replication lag under peak write, index size vs working set.
- Capacity is a hypothesis: verify with load tests at planned scale and re-review against measured peak on a cadence that matches your growth rate (quarterly for fast-growing services, less often for stable ones), and after any topology or workload change.

## Caching

- Cache derived/expensive data, never the source of truth: one authoritative copy plus explicit invalidation and rebuild paths.
- Measure hit rate AND its latency effect; monitor eviction rate and staleness. A 99% hit rate on a cold path can matter less than 80% on the hot path.
- Choose invalidation deliberately: jittered TTL, write-through/read-through, or versioned keys. Never expire correctness-critical unique data by TTL alone.
- Defend the stampede: single-flight, early recompute before TTL expiry, or jitter. Size the cache to hold the hot set; watch hot-key skew evicting everything else.
- Layer caches by cost and volatility (per-request → process → shared); each layer needs its own hit-rate and staleness budget.

## Database diagnosis (MySQL)

- Funnel order: metrics → slow query log → EXPLAIN of the worst queries → schema/index fix → re-measure with the identical benchmark.
- Read EXPLAIN for access type, index use, row estimates: hunt full scans, filesort/temp tables, and "Using index" (covering) queries. Test on real data — estimates lie on tiny tables.
- Common fixes in order: missing/overlapping indexes; functions or casts on indexed columns; SELECT * and unneeded columns; non-SARGable predicates; schema shape matching the read model.
- Before tuning blind, check engine internals: InnoDB buffer pool vs data set, connection pool sizing, replication lag, lock waits and deadlocks (performance_schema / sys schema).
- Use read replicas for analytics/backups only when the primary is write-bound; account for replica lag in read-your-writes semantics.
- Version behavior differs: MySQL 8.0 removed the query cache and changed defaults; 5.7 vs 8.0 optimizer and EXPLAIN differ. Test on the version you actually run.

## Metrics to collect

- RED per service: Rate, Errors, Duration (percentiles); USE per resource: utilization, saturation, errors.
- DB: slow-query count, buffer-pool hit ratio, lock waits, replication lag, connection utilization.
- Cache: hit rate, eviction rate, staleness/age, stampede events.

## Tuning workflow

- State the goal and SLA in percentile terms before touching anything; a session without a target metric is unverifiable.
- Find the bottleneck first (saturation), then the dominant cost of the hot path; fix the biggest measured cost, re-measure, repeat.
- Change one variable at a time — parallel changes make attribution impossible.
- Keep a run log: date, versions, environment, workload, results. The log is what makes tuning reproducible.

## Verification

- When performance is a claim or risk, every change ships with a before/after measurement from the relevant benchmark, including error rates; not faster = not verified. Skip the ritual where performance is not at stake.
- After deploy, watch latency percentiles, saturation, error rate, cache hit rate, and slow-query count across a representative window — a full peak cycle when one exists, otherwise a bounded observation matched to traffic patterns.

## Sources

- B. Gregg, *Systems Performance*, 2nd ed. (2020); the USE method at brendangregg.com.
- B. Schwartz et al., *High Performance MySQL* (O'Reilly).
- MySQL 8.0 reference manual: performance_schema, sys schema, EXPLAIN/optimizer.
