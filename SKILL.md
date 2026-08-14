---
name: engineering-excellence
description: "Production-grade engineering skill synthesizing all 14 classic software engineering books from ciembor/agent-rules-books. Covers complexity reduction, clean architecture, clean code, construction discipline, data-intensive systems, DDD (strategic and tactical), enterprise patterns, refactoring, production hardening, pragmatism, and legacy code."
model_instructions: rules
---

# Engineering Excellence — Full-Spectrum Codecraft

A comprehensive engineering skill distilled from 14 classic software books. This skill applies when **writing, reviewing, designing, refactoring, debugging, or planning production code**. It integrates SDD (Specification-Driven Development), TDD (Test-Driven Development), DDD (Domain-Driven Design), and DADD (Document & AI Driving Development) into one unified engineering framework.

---

## When to Use This Skill

| Primary Scenario | Apply These Rules |
|---|---|
| Designing a new module, API, or service | A Philosophy of Software Design + Clean Architecture + DDD Bounded Context |
| Writing or reviewing a function/method | Clean Code + Code Complete + Refactoring.Guru smells |
| Refactoring existing code | Refactoring + Working Effectively with Legacy Code + A Philosophy of Software Design |
| Production hardening and reliability | Release It! + Designing Data-Intensive Applications |
| Data model, storage, replication, or stream design | Designing Data-Intensive Applications + Enterprise Patterns |
| Domain modeling with business complexity | DDD + DDD Distilled + Implementing DDD |
| Enterprise application architecture | Enterprise Patterns + Clean Architecture |
| Working with messy, untested legacy code | Working Effectively with Legacy Code + Refactoring |
| Debugging and performance tuning | Pragmatic Programmer + Code Complete |
| AI-assisted code generation (DADD mode) | All books as context + pragmatic prompt engineering |

---

## Paradigm Integration: SDD -> TDD -> DDD -> DADD

### SDD (Specification-Driven Development)
- **Rule:** Before writing code, define the specification as a formal or structured contract.
- **When:** External API, library boundary, cross-team interface, compliance requirement.
- **Action:** Write interface contract, input/output schema, error semantics, and acceptance criteria before implementation.

### TDD (Test-Driven Development)
- **Rule:** Write the failing test first, then make it pass, then refactor.
- **When:** New behavior, bug fix, refactoring safety net, regression protection.
- **Action:** Red -> Green -> Refactor. Keep tests fast, deterministic, and at the right isolation level.

### DDD (Domain-Driven Design)
- **Rule:** Model the business domain first; let the domain drive technical decisions.
- **When:** Significant business complexity, evolving language, cross-team strategic alignment.
- **Action:** Identify Bounded Context -> Ubiquitous Language -> Aggregates -> Repositories -> Events.

### DADD (Document & AI Driving Development)
- **Rule:** Use structured documents as the backbone for AI code generation.
- **When:** Vibe Coding + low-code platform + AI agent collaboration.
- **Action:** Write structured document (spec -> design -> acceptance) -> Feed to AI agent -> Validate output -> Iterate.

### Integration Rules (All Four)
1. Start with **document/spec** when AI is involved (DADD first).
2. Start with **test** when behavior must be provably correct (TDD first).
3. Start with **domain model** when business complexity dominates (DDD first).
4. Start with **spec** when safety/correctness is paramount (SDD first).
5. In AI-assisted mode: Document -> Prompt -> Generate -> Test -> Refactor -> Commit.



## Book 1: A Philosophy of Software Design — Deep Modules and Complexity Reduction

**Source:** John Ousterhout — _A Philosophy of Software Design_

**Core Bias:** Working code does not mean simple design. Measure decisions by **cognitive load reduction**.

### Decision Rules
- **Prefer deep modules** — small interface, significant hidden complexity. Reject shallow wrappers, pass-through services, and thin facades that add a name without reducing reader burden.
- **Pull complexity downward** — make the module do the hard work so callers stay simple.
- **Hide information aggressively** — volatile decisions, internal representation, storage shape, protocols, and messy edge handling live inside the module boundary.
- **Design interfaces for callers** — callers should not need to know staging, setup sequences, mode flags, or internal choices.
- **Combine or split by total complexity**, not by size, habit, or runtime order.
- **Reduce exception surfaces** — define away invalid states instead of pushing defensive ceremony to every caller.
- **Use comments for what code cannot express** — rationale, invariants, contracts, hidden design decisions. Never narrate what the code already says.
- **Avoid temporal coupling** — if code must run in prepare/process/finalize order, the design leaks staging complexity.

### Trigger Rules
- A feature feels awkward or one change spreads across files -> look for missing information hiding or shallow modules.
- Adding a module, layer, wrapper, or callback -> prove it hides more complexity than it adds.
- Callers must know sequencing, setup, or internal workflow -> redesign the interface.
- Comments get long or explain confusing interfaces -> redesign the abstraction.

### Checklist
- [ ] Does every interface element hide enough complexity to justify its existence?
- [ ] Are important decisions localized and mutable internals protected?
- [ ] Are names precise and consistent, comments current and non-duplicative?
- [ ] Did common cases become automatic while rare controls stayed out of the common path?

---

## Book 2: Clean Architecture — Dependency Rule and Boundaries

**Source:** Robert C. Martin — _Clean Architecture_

**Core Bias:** Business policy survives frameworks, databases, UI, and vendor changes.

### Decision Rules
- **Source dependencies point inward.** Domain and use cases must not import frameworks, databases, web handlers, queues, or service clients.
- **Entities** guard enterprise invariants. **Use Cases** orchestrate one application action with plain I/O models.
- Frameworks, databases, web delivery, messaging, filesystems, clocks, and hardware sit behind **policy-owned ports** and **outer-layer adapters**.
- **Controllers, Presenters, Gateways, Handlers** translate; they do not own business logic.
- Organize by use case, feature, or business capability — not generic technical buckets or god services.
- Choose the **lightest enforceable boundary** — partial boundaries when full separation is too expensive.
- Test policy through entities, use cases, and boundary contracts **without** real frameworks or databases.
- Preserve behavior while improving dependency direction. Prefer incremental extraction over rewrites.
- Document architectural debt when it cannot be fixed safely now.

### Architecture Decision Rules
- Prefer **Hexagonal Architecture** (Ports and Adapters) for most services.
- Use **Layered Architecture** for simple CRUD with clear layer responsibilities.
- Use **CQRS** when read/write shapes diverge significantly.
- Use **Event-Driven Architecture** when loose coupling and asynchronous flow are required.
- For every architecture decision: document context, decision, consequences, and alternatives.

### Checklist
- [ ] Business rules independent from frameworks, databases, UI, services, and vendors?
- [ ] Dependencies point inward with ports owned by policy and details outside?
- [ ] Boundaries explicit and enforced in code, tests, packages, or build rules?
- [ ] Core tests run fast without real delivery, persistence, network, or hardware?
- [ ] Details remain replaceable without rewriting business rules?

---

## Book 3: Clean Code — Readability and Craftsmanship

**Source:** Robert C. Martin — _Clean Code_

**Core Bias:** Working code is not automatically clean code.

### Decision Rules
- **Write for local reasoning** — a reader understands the path without reconstructing hidden state.
- **Use precise names, one term per concept.** Rename when vocabulary hides intent.
- **Keep functions small, focused, at one abstraction level.** Tell the story top-down.
- **Keep parameters few and meaningful.** No boolean flags, output parameters, or grab-bag arguments.
- **Separate commands from queries.** A function that answers should not mutate.
- **Keep the happy path readable.** Isolate error handling, cleanup, and edge cases.
- **Expose behavior, not representation.** No train-wreck access or utility dumping grounds.
- **Keep construction, framework, persistence, and vendor details outside business behavior.**
- **Make public APIs small, explicit, and hard to misuse.**
- **Use comments only for rationale, constraints, warnings, or external contracts.** Never narrate code.
- **Treat tests as production code** — readable, deterministic, aligned with the behavior they protect.

### Trigger Rules
- Function mixes setup, validation, computation, and side effects -> split the phases.
- Comment explains control flow -> simplify names or structure instead.
- Function both mutates and answers -> separate the responsibilities.
- Boundary leaks framework or persistence quirks inward -> add or strengthen a local adapter.

### Checklist
- [ ] Can a reader follow the change locally without reconstructing hidden state?
- [ ] Are names and APIs carrying meaning without narration?
- [ ] Is mutation explicit and the happy path still clear?
- [ ] Did framework, persistence, and construction details stay behind boundaries?
- [ ] Do tests protect the changed behavior or contract?

## Book 4: Code Complete — Construction Discipline

**Source:** Steve McConnell — _Code Complete_

**Core Bias:** Construction quality is intentional, not accidental.

### Decision Rules
- Verify requirements, architecture, risks, conventions, and test approach **before large construction**.
- **Optimize for human readers** — clarity, locality, explicitness, visible control flow, consistent conventions.
- **Sketch complex routines in pseudocode** first, then convert to code; drop pseudocode comments that become obvious.
- **Make variable meaning explicit** — purpose-revealing names, small scope, deliberate initialization, named constants, strong types.
- **Keep control flow simple enough to verify** — shallow nesting, named predicates, clear loop semantics.
- **Validate input at trust boundaries.** Use assertions for programmer assumptions; validation for expected external failures.
- **Handle errors at the right abstraction** — preserve diagnostic context, keep normal path readable, never silently continue from corruption.
- **Build in small, verifiable increments** — integrate often enough to expose conflicts.
- **Tune performance only when evidence justifies it** — measure before and after, keep clarity unless tradeoff is measured.
- **Use table-driven logic for stable mappings** — only when the table is clearer than code.

### Checklist
- [ ] Requirements, architecture fit, risks, and conventions are clear enough?
- [ ] Names, routines, data, and comments reduce reader effort?
- [ ] Inputs, errors, assertions, and trust boundaries are deliberate?
- [ ] Control flow is simple enough to inspect?
- [ ] The change is small enough to verify and would stand up to careful review?

---

## Book 5: Designing Data-Intensive Applications — Data Systems

**Source:** Martin Kleppmann — _Designing Data-Intensive Applications_

**Core Bias:** Do not design distributed data behavior as if every write, read, queue, and replica were local, ordered, fresh, and exactly-once.

### Decision Rules
- Make **core trade-offs explicit**: source of truth, consistency expectation, retry behavior, duplicate/reordered work, partial failure, data evolution.
- Treat crashes, partial writes, duplicates, timeouts, stale reads, and unknown downstream success as normal inputs.
- Describe load with **concrete request rates, data volume, access patterns, latency, throughput, percentiles** before changing architecture.
- Choose data models from **relationships, access patterns, consistency needs, update locality, and evolution pressure.**
- Treat indexes, caches, search copies, read models, materialized views, and denormalized fields as **derived data** with explicit propagation, lag, observability, and rebuild paths.
- Make commands, jobs, events, stream processors safe under **retry and replay** — deduplication keys, idempotent transitions, or explicit recovery contracts.
- Preserve only the **ordering the business logic actually needs** — scope it per key, stream, or partition.
- Separate **commands, events, durable logs, streams, and materialized views.** Events describe facts; consumers tolerate lag, duplicates, restart, and versioned payloads.
- Design schemas, APIs, messages, events as **evolving contracts** across old/new readers/writers/stored data.
- Choose replication topology from **write topology, latency, failure tolerance, lag, failover, and conflict handling** needs.
- Partition by **workload-relevant locality** — be explicit about hot-key, skew, routing, rebalancing costs.
- Match **transactions and isolation to invariants** — scope atomicity, commit behavior, recovery, reconciliation explicitly.
- Treat **network delay, partitions, duplicates, pauses, stale leaders, timeouts, wall-clock uncertainty** as assumptions needing a fault model.
- Use **linearizability, total order broadcast, atomic commit, consensus** only where the coordination problem truly requires agreement.
- Make batch/stream processing **recomputable and recoverable** — define inputs, outputs, checkpoints, event time, processing time, windows, late data, joins, and guarantees.

### Checklist
- [ ] Source of truth and derived representations are explicit?
- [ ] Consistency expectations, durability points, staleness, and conflict rules are concrete?
- [ ] Retries, duplicate delivery, replay, reordering, timeouts, crashes, and unknown success are handled?
- [ ] Schemas, APIs, messages, events evolve safely across mixed versions?
- [ ] Storage, indexing, replication, partitioning match the workload?
- [ ] Transaction isolation and coordination choices protect named invariants?
- [ ] Events, logs, streams, batch jobs, and projections are replayable or have explicit repair paths?
- [ ] Lag, retries, failures, rebuilds, and repair paths are observable?

---

## Book 6-8: Domain-Driven Design — Strategic and Tactical

**Sources:** Eric Evans — _Domain-Driven Design_; Vaughn Vernon — _DDD Distilled_ and _Implementing DDD_

**Core Bias:** Domain behavior, code, tests, documents, and team language stay aligned inside explicit Bounded Contexts.

### Strategic Design Rules
- Use a model only when it organizes domain knowledge, clarifies communication, and drives implementation.
- Maintain **one Ubiquitous Language per Bounded Context** across names, tests, docs, and conversations.
- Put the most modeling effort into the **Core Domain**. Keep Supporting and Generic subdomains simpler.
- Define every Bounded Context explicitly. The same word in different contexts = potentially different concepts.
- Choose context relationships deliberately: Partnership, Shared Kernel, Customer/Supplier, Conformist, Anticorruption Layer, Open Host Service, Published Language, Separate Ways.
- Use **Context Maps**, tests, and active communication to protect model integrity across contexts.
- Distill and protect the Core Domain by strategic value from generic subdomains and infrastructure.

### Tactical Design Rules
- **Entities** — stable identity, protect meaningful state transitions, expose intention-revealing behavior.
- **Value Objects** — immutable, self-validating, compared by value. Promote primitives that carry domain meaning.
- **Domain Services** — for important operations with no natural Entity or Value Object home.
- **Aggregates** — invariant and transactional consistency boundaries. Keep small, root-protected, identity-reference other Aggregates, one per transaction by default.
- **Repositories** — for Aggregate Roots, not tables. Domain-oriented interfaces, hide persistence mechanics.
- **Domain Events** — meaningful past-tense business facts. Not for property changes or poor Aggregate design.
- **Factories** — hide complex creation logic; prevent partially-formed objects from escaping.
- **Modules** — conceptual cohesion within Bounded Context.

### Application Layer Rules
- **Application Services** coordinate use cases: load Aggregates, invoke domain behavior, persist results, publish events. They are NOT the domain model.
- Keep **frameworks, persistence, transport formats, REST representations, and infrastructure types** out of the domain model.
- Use **DTOs, projections, use-case queries** when client needs differ from Aggregate shape.
- Test domain behavior directly: invariants, state transitions, Value Object validation, Events as outcomes.

### DDD Distilled — Minimal Effective DDD
- Apply full tactical DDD only where invariants, lifecycle, language complexity, or integration risk justify it.
- Do not force DDD on simple CRUD, generic subsystems, or mainly technical problems.
- Select integration style by business coupling: RPC, REST, messaging — each has different translation and failure semantics.
- Keep integration contracts separate from internal models; test boundary translations.

### Implementing DDD — Practical Guardrails
- Name the Bounded Context before interpreting terms, modules, services, repositories, or events.
- Reference other Aggregates by identity; avoid large connected object graphs.
- Use Event Sourcing only when event sequence is the right persistence model.
- Keep command behavior separate from query models when consistency, performance, or representation justifies it.

### Checklist
- [ ] Is domain behavior explicit in the model, not hidden in delivery, persistence, or integration code?
- [ ] Do code, tests, documents, and conversations use one language inside each Bounded Context?
- [ ] Do tactical patterns protect identity, value semantics, lifecycle, invariants, and responsibility?
- [ ] Does every cross-context integration have an explicit relationship, translation strategy, and boundary test?
- [ ] Are Aggregates small, root-protected, identity-linked, and usually one per transaction?
- [ ] Is the Core Domain visible and protected from supporting complexity and infrastructure?
- [ ] Do tests read like executable examples of the model?
---

## Book 9: Patterns of Enterprise Application Architecture — Enterprise Patterns

**Source:** Martin Fowler — _Patterns of Enterprise Application Architecture_

**Core Bias:** Make responsibility ownership explicit; do not let framework, ORM, or transport shape the design.

### Decision Rules
- **Separate presentation, workflow, domain logic, data source, transactions, concurrency, and integration.**
- Choose business logic pattern by force: **Transaction Script** for simple flows, **Table Module** for set logic, **Domain Model** for rich rules/lifecycle.
- Use a **Service Layer** for application operations, transaction boundaries, and orchestration.
- Choose persistence patterns deliberately: **Repository** (domain terms), **Data Mapper** (SQL hidden outside domain), **Gateway** (record/table access), **Active Record** (only for simple domains).
- Keep identity, write coordination, and loading visible: **Identity Map**, **Unit of Work**, **Lazy Load**.
- Design concurrency explicitly: **Optimistic Lock** (detect conflicts), **Pessimistic Lock** (prevent conflicts).
- Keep presentation code focused on input/rendering/routing — business rules stay out of controllers/views.
- Access external systems through boundaries — translate partner formats into internal concepts.
- Choose session state deliberately: client, server, or database — each has integrity, security, scaling, cleanup trade-offs.

### Trigger Rules
- Domain behavior in controllers, views, SQL scripts, DTOs, or framework glue → move to owning layer.
- One class coordinates rendering, validation, SQL, transactions, domain rules, and external calls → split by responsibility.
- ORM model doubles as Aggregate, DTO, and Service → treat as a forbidden-pattern blocker.
- Remote API looks like local object collaboration → redesign as coarse use-case contract with DTO translation.

### Checklist
- [ ] Presentation, workflow, domain, persistence, transaction, concurrency, integration, session responsibilities separated intentionally?
- [ ] Business logic pattern matches actual complexity rather than habit or framework shape?
- [ ] Transaction ownership explicit, short, kept out of hidden helpers or remote-call spans?
- [ ] Remote and integration boundaries coarse, translated, version-aware, failure-aware?
- [ ] Tests aligned to the responsibility that owns each behavior?

---

## Book 10-11: Refactoring and Refactoring.Guru — Behavior-Preserving Transformation

**Sources:** Martin Fowler — _Refactoring_; Refactoring.Guru

**Core Bias:** Refactoring is behavior-preserving design work in small steps — not cleanup, not rewrite, not feature addition.

### Decision Rules
- **Preserve observable behavior.** Isolate behavior changes from structural changes.
- **Work in small, reversible, buildable, testable, reviewable steps.**
- **Establish a safety net** before risky refactoring — characterization tests for unclear behavior.
- **Use preparatory and follow-up refactoring** around feature work: reshape friction, make change, clean up.
- **Refactor the current blocking smell**, not every smell in sight.
- **Diagnose the smell before choosing the technique** — symptom, maintenance cost, expected cleaner state, verification path, stop condition.
- Use the **Rule of Three** — tolerate uncertain duplication twice, refactor the third occurrence.
- **Stop when the named smell is gone or materially reduced.** Record new smells separately.

### Smell Categories and Treatments
- **Bloaters** (long method, large class, long parameter list, primitive obsession, data clumps) → extraction, parameter/object modeling, responsibility splits.
- **OO Abusers** (switch statements, temporary field, refused bequest, alternative classes) → polymorphism, state/strategy, subclass extraction.
- **Change Preventers** (divergent change, shotgun surgery, parallel inheritance) → move behavior/data toward the owner of the changing concept.
- **Dispensables** (comments, dead code, lazy class, data class, duplicate code, speculative generality) → delete, inline, or consolidate.
- **Couplers** (feature envy, inappropriate intimacy, message chains, middle man) → reduce navigation and private knowledge.

### Checklist
- [ ] Is this change clearly refactoring, feature work, or bug fixing?
- [ ] Which smell was diagnosed, and what cost did it create?
- [ ] Was the smallest suitable treatment used before riskier structure?
- [ ] Did behavior stay preserved under relevant checks?
- [ ] Did the named smell become materially better?
- [ ] Did the change avoid speculative abstraction and mechanical pattern use?

---

## Book 12: Release It! — Production Hardening and Stability

**Source:** Michael T. Nygard — _Release It!_

**Core Bias:** A passing happy path is not production readiness.

### Decision Rules
- Assume every dependency, queue, cache, timeout, and degraded state **can fail in slow, partial, or prolonged ways.**
- Prefer designs that **fail visibly, limit blast radius, shed load, preserve core service, and enable diagnosis.**
- Put **explicit time limits** on outbound calls and waits. No infinite waits.
- **Retry only when safe** — bound count and total time, use backoff/jitter, do not retry validation errors.
- Isolate failures with **circuit breakers, fast failure, bulkheads, separate resource pools.**
- Design overload behavior explicitly — **back pressure, finite queues, demand limits, load shedding.**
- Use stability patterns by failure mode: steady state, fail fast, let-it-crash (with supervision), handshaking, decoupling middleware, governors.
- **Budget scarce resources explicitly**, release deterministically, avoid holding locks across slow calls.
- **Treat external input as untrusted** — validate syntax, shape, business plausibility, semantics.
- Build **observability into boundaries** — structured context, correlation IDs, latency, throughput, error, saturation, queue depth, retry, breaker, health signals.
- Make **startup, health checks, migrations, deployments, and controls** idempotent, restartable, observable, stoppable.
- Include **security and hostile traffic** in production readiness.

### Stability Pattern Reference
| Pattern | Best For | Key Property |
|---|---|---|
| Circuit Breaker | Dependency isolation | Open/closed/half-open state |
| Bulkhead | Resource contention | Separate thread/connection pools |
| Timeout | All outbound calls | Explicit deadline per call |
| Retry with Backoff | Transient failures | Bounded, jittered |
| Load Shedding | Overload protection | Graceful degradation |
| Fast Failure | Resource exhaustion | Fail before cascading |
| Handshaking | Capacity awareness | Back pressure signal |

### Checklist
- [ ] Explicit timeouts and no infinite waits?
- [ ] Retries safe, bounded, backed off, and not duplicated across layers?
- [ ] Queues, buffers, pools, caches, logs, payloads bounded?
- [ ] Failure isolated with breakers, bulkheads, fast failure, or load shedding?
- [ ] External input validated before affecting state, caches, queues, or downstream?
- [ ] Diagnostics cover logs, metrics, health, correlation, dependencies, saturation?
- [ ] Startup, deployment, migrations restartable, observable, recoverable?
---

## Book 13: The Pragmatic Programmer — Pragmatic Engineering

**Source:** Andrew Hunt and David Thomas — _The Pragmatic Programmer_

**Core Bias:** Do not optimize only for the local edit or familiar ritual. Own the outcome.

### Decision Rules
- **Be pragmatic, not dogmatic** — choose what improves real outcomes for users, risks, and codebase.
- **Own the result** — surface tradeoffs, risks, and avoidable costs instead of blaming tools or schedule.
- **Keep one authoritative representation** for each system fact (DRY).
- **Preserve orthogonality** — unrelated concerns should not change together.
- **Keep volatile decisions reversible** until evidence justifies commitment.
- **Use domain vocabulary and small domain languages** only when they make rules clearer to validators.
- **Prefer thin end-to-end tracer bullets** over piles of isolated pieces.
- **Use prototypes to learn, not to pretend work is done.** State what is proven and what is not.
- **Dig for real requirements** — separate durable needs from current solutions and growing prose.
- **Automate repetitive, error-prone, easy-to-forget work** — builds, tests, linting, formatting, deployment, validation.
- **Shorten feedback loops** with relevant tests, cheap early signals before late expensive surprises.
- **Make contracts, assumptions, invariants, obligations explicit** and close to the abstractions they protect.
- **Distinguish failure types** — programmer errors, contract violations, impossible states, expected domain failures, retryable, recoverable, permanent.
- **Treat resource ownership as a contract** — release every acquired allocation on success and failure.
- **Prefer inspectable plain text, open formats, version-aware configuration** when longevity, diffability, migration matter.
- **Debug from reproduced facts** — observe, isolate, explain, fix, verify before guessing.
- **Break work into small deliverable increments** with honest uncertainty and visible risk.
- **Apply the broken windows rule** — fix or visibly contain small quality decay before it normalizes.

### Checklist
- [ ] One authoritative owner for each system fact?
- [ ] Unrelated concerns independent and volatile choices reversible?
- [ ] Working feedback exists for risky assumptions?
- [ ] Contracts, failures, diagnostics, resources, and cleanup explicit?
- [ ] State, concurrency, ordering, and coupling visible?
- [ ] Repeatable work automated, versioned, and aligned with shared checks?
- [ ] Tests automatic, relevant, and run before calling the change done?
- [ ] Names, comments, docs, scripts, tests, and commits communicate intent?
- [ ] Touched area better or explicitly contained?

---

## Book 14: Working Effectively with Legacy Code — Legacy Code Strategy

**Source:** Michael Feathers — _Working Effectively with Legacy Code_

**Core Bias:** Gain control before improving design.

### Decision Rules
- **Treat any area without trustworthy tests as legacy code.** Do not start with rewrite.
- Follow the **legacy loop**: identify change point -> check existing protection -> characterize -> find/create seam -> break blocking dependency -> change -> refactor locally.
- **State the requested behavior change and the behavior that must remain.**
- **Choose test points** by tracing effects outward from the change point.
- **Use the smallest seam** that allows substitution, observation, or interception.
- **Break dependencies deliberately** — expose hidden inputs, hard outputs, hard construction, globals, statics, ambient context, framework callbacks.
- **Keep behavior changes, structural refactorings, and cleanup separate.**
- When direct edits are risky: **sprout method, sprout class, wrap method, wrap class, extract-and-override.**
- For hard-to-test methods: **split construction from use, extract side effects behind collaborators, carve pure computation first.**
- **Reject changes that expand hidden dependencies**, mock around untestable structure without improving it, or rename while leaving real knots intact.

### Dependency-Breaking Techniques
| Technique | Barrier Targeted |
|---|---|
| Extract Interface | Hard-coded concrete dependency |
| Adapt Parameter | Specific method parameter hard to construct |
| Extract and Override Call | Hard-coded method call in constructor |
| Parameterize Constructor | New expression in constructor |
| Encapsulate Global | Global/static state |
| Introduce Instance Delegator | Static method calls |

### Checklist
- [ ] Untested or weakly tested area treated as legacy risk?
- [ ] Behavior delta and behavior-to-preserve stated?
- [ ] Uncertain current behavior characterized or explicitly observed?
- [ ] Tests close enough and fast enough to diagnose the change?
- [ ] Smallest useful seam chosen with sensing vs separation clear?
- [ ] Blocking dependency reduced without expanding hidden dependencies?
- [ ] Behavior change, refactoring, and cleanup kept separate?
- [ ] Touched area more understandable, testable, or changeable?

---

## Multi-Book Decision Trees

### Decision Tree A: How to Start a New Feature

1. Is this a business-critical, complex domain?
   - YES -> Apply DDD: identify Bounded Context -> Ubiquitous Language -> Aggregates -> Repositories -> Events. Set up Clean Architecture: ports, adapters, dependency direction. Apply A Philosophy of Software Design: deep modules, information hiding.
   - NO -> Is this CRUD or simple flow?
     - YES -> Use Transaction Script + Gateway/Active Record (Enterprise Patterns). Keep Clean Code rules active.
     - NO -> Use Service Layer + Domain Model (Enterprise Patterns). Add Clean Architecture boundaries if likely to grow.

2. Regardless of path:
   - Write tests first (TDD) or write specification + generate (DADD)
   - Apply Code Complete construction discipline
   - Use Pragmatic Programmer tracer bullet approach

### Decision Tree B: How to Modify Existing Code

1. Does the area have trustworthy tests?
   - YES -> Apply Refactoring + Clean Code rules. Diagnose smell -> Choose smallest treatment -> Change -> Verify.
   - NO -> Apply Legacy Code rules: State behavior change -> Characterize -> Find seam -> Break dependency -> Change -> Refactor.

2. Is the change high-risk (production-critical, dependency-heavy)?
   - YES -> Also apply Release It! production hardening rules.
   - NO -> Proceed with small incremental steps.

3. After change, did you make the area better?
   - One smell removed? One concept clarified? One dependency broken?
   - If not, apply Pragmatic Programmer broken windows rule.

### Decision Tree C: Architecture and Data Design

1. Is this a distributed system or microservice?
   - YES -> Designing Data-Intensive Applications: define source of truth -> consistency model -> replication -> partitioning. Apply Release It! stability patterns. Apply Enterprise Patterns remote boundaries.
   - NO -> Monolith within single process? Apply Clean Architecture + Enterprise Patterns layers.

2. Does the data model involve complex queries, reporting, or event sourcing?
   - YES -> CQRS: separate command model (DDD Aggregates) from query model (read projections). Event sourcing only if audit trail/event-driven integration provides concrete value.
   - NO -> Standard Repository pattern sufficient.

### Decision Tree D: AI-Assisted Development (DADD Mode)

1. Write structured document:
   - Specification (what, not how)
   - Design decisions (architecture, patterns, boundaries)
   - Acceptance criteria (tests that must pass)

2. Feed to AI agent with specific context:
   - Which books rules should the AI follow?
   - Aggregate mini.md files for selected books as skill context

3. Generate -> Review -> Test -> Refactor:
   - Validate output against Clean Code, Code Complete, Refactoring rules
   - Check architecture dependency direction (Clean Architecture)
   - Ensure domain model alignment (DDD if applicable)

4. Iterate: Update document -> Regenerate -> Revalidate. Apply Pragmatic Programmer: prototype to learn, not pretend work is done.

---

## Universal Production Checklist

Before calling any change complete:

- [ ] **Domain behavior in the model** — not in controllers, persistence, views, or scripts?
- [ ] **Dependencies point inward** — core code free of framework, database, and infrastructure imports?
- [ ] **One authoritative source** — no duplicate or contradictory knowledge (DRY)?
- [ ] **Orthogonal changes** — unrelated concerns do not change together?
- [ ] **Clear, precise names** — one term per concept, unambiguous, revealing intent?
- [ ] **Mutation and side effects visible** — commands separated from queries?
- [ ] **Happy path readable** — errors and edge cases explicit but not noisy?
- [ ] **Safety net present** — tests pass or verification gap recorded?
- [ ] **Small, reviewable step** — change is focused, not a mixed patch?
- [ ] **Reversible or confirmed choice** — uncertain decisions made smaller or validated?
- [ ] **Timeouts, retries, boundaries explicit** — no infinite waits, safe retry?
- [ ] **Schema/API evolution considered** — old readers, old data, migration path?
- [ ] **Work left in better condition** — one smell removed, one concept clarified, one dependency broken?

---

## Quick Reference: Rule Application by Scenario

| Scenario | Primary Books | Secondary Books |
|---|---|---|
| New module/API design | A Philosophy of Software Design, Clean Architecture | DDD, Enterprise Patterns |
| Writing a function | Clean Code, Code Complete | Refactoring.Guru |
| Domain modeling | DDD (all 3), DDD Distilled | Enterprise Patterns |
| Refactoring existing code | Refactoring, Refactoring.Guru | A Philosophy of Software Design |
| Legacy code changes | Working Effectively with Legacy Code | Refactoring |
| Production hardening | Release It! | Pragmatic Programmer |
| Data model/DB events | Designing Data-Intensive Applications | Enterprise Patterns |
| Code review | Clean Code, Refactoring.Guru | Code Complete |
| Architecture review | Clean Architecture, DDD (Bounded Context) | Enterprise Patterns |
| Debugging | Pragmatic Programmer | Code Complete |
| Performance tuning | Code Complete, Pragmatic Programmer | Designing Data-Intensive Applications |
| AI-assisted generation (DADD) | All books contextually | Pragmatic Programmer (validate output) |
| Planning and estimation | Pragmatic Programmer, Code Complete | Release It! |

---

## Version-Sensitivity Map

Different book versions target different levels. Use this to scope your application:

| Level | Books to Focus | Context Budget |
|---|---|---|
| **Nano** (ultra-tight) | Clean Code (nano), Refactoring (nano), Pragmatic Programmer (nano) | ~1.5 KB each |
| **Mini** (default skill) | All books at mini level — this file covers all | Full skill content |
| **Full** (deep reference) | Full .md files from agent-rules-books for each relevant book | Variable |
| **Audit/Review** | Release It! + Designing Data-Intensive Applications full files | Large |

---

*Skill synthesized from ciembor/agent-rules-books v0.5 — 14 classic software engineering books distilled into one unified, production-grade engineering framework. Integrates SDD, TDD, DDD, and DADD paradigms for comprehensive coverage of modern software development practices.*


