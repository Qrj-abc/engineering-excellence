# Architecture & Domain — Boundaries, Models, Enterprise Patterns

Scope: structuring systems, defining module and service boundaries, modeling business domains, and choosing persistence and integration shapes. Sources: Clean Architecture (Martin); Domain-Driven Design (Evans, Vernon); Patterns of Enterprise Application Architecture (Fowler).

## Decision Rules

### Set dependency direction (Clean Architecture)
- Source dependencies point inward: domain and use cases never import frameworks, databases, UI, queues, or vendor clients.
- Entities guard enterprise invariants; use cases orchestrate one application action with plain I/O models.
- Controllers, presenters, gateways, and handlers translate; they own no business logic.
- Frameworks, databases, delivery, messaging, filesystems, and clocks sit behind policy-owned ports and outer adapters.
- Organize by use case, feature, or business capability — not generic technical buckets or god services.
- Choose the lightest enforceable boundary: full separation where policy is precious, partial boundaries elsewhere.
- Preserve behavior while improving dependency direction; prefer incremental extraction over rewrites.
- Record every architecture decision with context, decision, consequences, and alternatives.
- Test policy through entities, use cases, and boundary contracts without real frameworks or databases.
- Use cases own application rules: authorization, transaction scope, and response assembly live there — not in entities or controllers.
- Boundaries must be enforced in code, tests, packages, or build rules — an unenforced boundary is a suggestion.

### Model the domain (DDD)
- One Ubiquitous Language per Bounded Context: code, tests, docs, and conversation say the same thing.
- The same word in different contexts is likely a different concept; name contexts explicitly.
- Concentrate modeling effort on the Core Domain; keep supporting and generic subdomains deliberately simpler.
- Choose context relationships explicitly: partnership, shared kernel, customer/supplier, conformist, anticorruption layer, open host service, published language, separate ways.
- Entities: stable identity, intention-revealing transitions. Value Objects: immutable, self-validating, compared by value.
- Aggregates: the invariant and transactional consistency boundary — small, root-protected, referenced by identity, one per transaction by default.
- Repositories serve aggregate roots, not tables; factories hide creation complexity; domain events are past-tense business facts, not property changes.
- Application services coordinate use cases (load aggregates, invoke behavior, persist, publish events); they are not the domain model.
- Keep frameworks, persistence, transport formats, and infrastructure types out of the domain.
- Module boundaries follow domain concepts within a context, not technical kinds.
- Apply full tactical DDD only where invariants, lifecycle, language, or integration risk justify it; CRUD stays simple.

### Apply enterprise patterns (PoEAA)
- Separate presentation, workflow, domain logic, data source, transactions, concurrency, and integration.
- Choose the business-logic pattern by force: Transaction Script (simple flows), Table Module (set logic), Domain Model (rich rules and lifecycle).
- Use a Service Layer for application operations, transaction boundaries, and orchestration.
- Pick persistence deliberately: Repository (domain terms), Data Mapper (SQL hidden outside the domain), Gateway (table access), Active Record (simple domains only).
- Keep identity, write coordination, and loading visible: Identity Map, Unit of Work, Lazy Load.
- Design concurrency explicitly: optimistic lock detects conflicts; pessimistic lock prevents them.
- Access external systems through coarse, translated, version-aware, failure-aware boundaries; never look like local object calls.
- Choose session state deliberately: client, server, or database — each trades integrity, security, scaling, and cleanup.
- Transaction ownership is explicit, short, and kept out of hidden helpers and remote-call spans.

### Public contracts and evolution
- Keep a compatibility matrix for each public contract — existing vs. new clients — and update it on every change; a contract is binding once published, even with no known consumers yet.
- Prefer additive changes first: new fields, endpoints, or events that old clients can ignore; break compatibility only when carrying the old shape costs more than the migration.
- Set an explicit version and deprecation policy: announce, keep old versions alive for an agreed window, route deprecated calls to observability, and remove only after migration evidence.
- Test contracts at the boundary — consumer-driven contracts or boundary tests against the published shape — so a change that breaks an existing consumer fails in CI, not in production.
- Roll out mixed versions deliberately: clients and servers of different versions coexist for the overlap window, so scope breaking changes behind versioned endpoints or payloads.
- Treat auth and token claims as contracts: adding a claim is additive, but removing, renaming, or changing the meaning of a claim can break every consumer that reads it.
- Make migration and rollback explicit for schema and contract changes: forward-compatible writes, reversible steps, and a defined rollback that restores the previous contract.

## Applicability Guardrails
- Clean Architecture pays off when policy must outlive frameworks and vendors; a small CRUD app does not need five layers.
- DDD is for business complexity, not technical problems; forcing aggregates onto CRUD adds ceremony without protection.
- Prefer a monolith first; distributed boundaries are cost, not virtue — extract services at a real seam.
- Full context mapping is overkill for a single team and context; draw only the map you need.
- Use each pattern only when you can name the force it answers; habit and framework shape are not forces.
- Hexagonal (ports and adapters) for most services; layered for simple CRUD; CQRS when read/write shapes diverge; events when loose coupling dominates.
- Choose integration style (RPC, REST, messaging) by business coupling; each has different translation and failure semantics.
- Document architectural debt when it cannot be fixed safely now.

## Common Tensions
- Purity of boundaries vs. delivery speed — start lean, add boundaries when change frequency justifies them.
- Model richness vs. simplicity — model only what the business language needs; YAGNI applies to models too.
- Aggregate consistency vs. throughput — one transaction per aggregate fights high-write paths; split aggregates only when invariants allow.
- ORM shape vs. domain shape — the ORM model doubling as aggregate, DTO, and service is a forbidden pattern.
- Framework conventions vs. policy independence — framework idioms leak inward; adapt at the ports.
- Shared kernel convenience vs. team coupling — shared code couples teams; prefer contracts and anticorruption layers.
- Ubiquitous Language purity vs. legacy vocabulary — rename gradually and map old terms to new ones during migration.
- Layered vs. hexagonal style — layers leak upward when controllers touch repositories directly; enforce with package boundaries or lint rules.

## Verification
- Dependency check: draw the import graph — does anything in the core reference an outer layer?
- Core tests run without a real framework, database, or network: proof of independence.
- Context map exists; each cross-context integration names its relationship, translation strategy, and boundary test.
- For each aggregate, name the invariant it protects and confirm the transaction scope matches it.
- For each pattern in use, name the force it answers; if you cannot, reconsider it.
- A business-rule change touches the domain layer only — controllers, persistence, and delivery stay untouched.
- Tests read like executable examples of the model; invariant violations surface in fast unit tests.
