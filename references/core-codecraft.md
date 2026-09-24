# Core Codecraft — Writing, Refactoring, and Legacy Change

Scope: module- and function-level writing, review, refactoring, and legacy modification. Sources: A Philosophy of Software Design (Ousterhout); Clean Code (Martin); Code Complete (McConnell); Refactoring + Refactoring.Guru (Fowler); The Pragmatic Programmer (Hunt/Thomas); Working Effectively with Legacy Code (Feathers).

## Decision Rules

### Design interfaces and modules (APoSD)
**Sources:** [aposd]
- Prefer deep modules: small interface, significant hidden complexity. Reject pass-through wrappers that add a name without removing caller work.
- Pull complexity downward: the module does the hard work so callers stay simple.
- Hide information: internal representation, storage shape, protocols, and volatile decisions stay inside the boundary.
- Define away invalid states; shrink exception surfaces instead of pushing defensive ceremony onto every caller.
- Design for the common case: make it automatic; keep rare controls and setup sequences out of the main path.
- Comments state what code cannot: rationale, invariants, contracts. Never narrate the code.
- Combine or split modules by total complexity, not by size, habit, or runtime order.
- Avoid temporal coupling: if callers must run prepare/process/finalize, the interface leaks staging complexity.
- Redefine errors as normal cases where possible — an absent row returning empty beats throwing to callers who cannot act on it.
- When one change spreads across many files, suspect missing information hiding; when one file changes for many reasons, split it.

### Write functions (Clean Code + Code Complete)
**Sources:** [clean-code] [code-complete]
- One term per concept; names reveal intent; rename when vocabulary misleads.
- Small functions at one abstraction level; the happy path reads top-down like a story.
- Few meaningful parameters: no boolean flags, output parameters, or grab-bag arguments.
- Separate commands from queries: a function that answers should not mutate.
- Keep construction, framework, persistence, and vendor details outside business behavior.
- Validate at trust boundaries; use assertions for programmer assumptions, validation for expected external failures.
- Handle errors at the right abstraction: preserve diagnostic context, keep the normal path readable, never silently continue from corruption.
- Keep control flow shallow enough to verify; use named predicates; table-driven logic only when clearer than code.
- Sketch complex routines in pseudocode first; drop comments that become obvious after coding.
- Treat tests as production code: readable, deterministic, aligned with the behavior they protect.
- Isolate error handling and edge cases so the happy path stays uncluttered.

### Stay pragmatic (Pragmatic Programmer)
**Sources:** [pragprog]
- Keep one authoritative representation per system fact (DRY); keep unrelated concerns orthogonal.
- Keep volatile decisions reversible until evidence justifies commitment.
- Automate repetitive, error-prone work; shorten feedback loops with cheap early signals.
- Debug from reproduced facts: observe, isolate, explain, fix, verify — before guessing.
- Prefer tracer bullets over piles of isolated pieces; prototypes are for learning — say what is proven and what is not.
- Apply the broken-windows rule: fix or visibly contain small quality decay before it normalizes.
- Distinguish failure types: programmer errors, contract violations, expected domain failures, retryable vs. permanent.
- Treat resource ownership as a contract: release every acquisition on success and failure.
- Break work into small deliverable increments with honest uncertainty and visible risk.

### Refactor safely (Fowler / Refactoring.Guru)
**Sources:** [refactoring] [refactoring-guru]
- Refactoring preserves observable behavior; keep behavior changes and structural refactoring in separate, individually verifiable steps — each step builds and passes checks, and they need not be separate tasks or commits.
- Work in small, reversible, buildable, testable, reviewable steps.
- Establish a safety net first; characterize behavior you do not fully understand before changing it.
- Diagnose the smell before choosing the technique: symptom, maintenance cost, target shape, verification, stop condition.
- Refactor the current blocking smell, not every smell in sight; stop when it is materially reduced.
- Use the Rule of Three: tolerate uncertain duplication twice; refactor the third occurrence.
- Treat smell categories by cause: bloaters → extraction; OO abusers → polymorphism/state; change preventers → move behavior to the owner of the changing concept; dispensables → delete or consolidate; couplers → reduce navigation and private knowledge.
- Prefer the smallest suitable treatment before riskier structure; mechanical pattern application is itself a smell.

### Change legacy code (Feathers)
**Sources:** [welc]
- Untested code is legacy; do not start with a rewrite.
- State the behavior change and the behavior that must remain before touching code.
- Legacy loop: find change point → check existing protection → characterize → find a seam → break the blocking dependency → change → refactor locally.
- Choose the smallest seam that enables substitution, observation, or interception.
- When direct edits are risky: sprout method/class, wrap method/class, extract-and-override.
- Keep behavior changes, structural refactoring, and cleanup in separate steps.
- Reject changes that expand hidden dependencies or mock around untestable structure without improving it.

### Review any change
**Sources:** (synthesis)
- Read the diff as a stranger: can you explain every hunk without the author?
- One fact, one owner: no duplicate or contradictory knowledge introduced.
- The change is small enough to verify and would survive a careful review.
- Names, comments, tests, and the commit tell a coherent story.

## Applicability Guardrails
**Sources:** (synthesis)
- Deep-module and information-hiding advice targets stable, reused boundaries; over-engineering one-off glue is waste.
- Strict command/query separation and zero-flags are guidelines: private helpers may relax them when the public surface stays clean.
- Full refactoring ceremony is disproportionate for throwaway or prototype code — label it as such explicitly.
- Legacy techniques are for code under active change; do not mass-refactor dormant legacy just because it is ugly.
- Refactor where change is coming; speculative abstraction is itself a smell.
- Conventions beat personal taste: codebase-level consistency outranks an individual's preferred style.

## Common Tensions
**Sources:** (synthesis)
- Information hiding vs. debuggability — document hidden decisions and expose diagnostics instead of exposing internals.
- DRY vs. premature abstraction — duplication is cheaper than a wrong abstraction; the Rule of Three arbitrates.
- Deep modules vs. framework idioms — frameworks force shallow shapes; wrap them at the boundary, do not fight inside.
- Small steps vs. momentum — interleave refactoring with features via preparatory and follow-up refactors.
- Purity of style vs. delivery pressure — the pragmatic test: does the rule reduce real cognitive load or just ceremony?

## Verification
**Sources:** (synthesis)
- Interface: a caller can use the module without knowing internals; interface shrank while capabilities stayed.
- Function: read it top-down; happy path clear, mutation explicit, no narrating comments, error paths isolated.
- Refactor: behavior preserved under the relevant tests; the named smell is materially reduced.
- Legacy: smallest seam used; hidden dependencies did not grow; the touched area is more testable than before.
- Universal: one fact has one owner; touched code is left better or visibly contained.

## Sources

- [aposd] J. Ousterhout, *A Philosophy of Software Design* — https://web.stanford.edu/~ouster/cgi-bin/aposd.php
- [clean-code] R. Martin, *Clean Code* (book; no open edition)
- [code-complete] S. McConnell, *Code Complete*, 2nd ed. (book; no open edition)
- [refactoring] M. Fowler, *Refactoring*, 2nd ed. — https://martinfowler.com/books/refactoring.html
- [refactoring-guru] Refactoring.Guru — https://refactoring.guru/refactoring
- [pragprog] A. Hunt & D. Thomas, *The Pragmatic Programmer*, 20th Anniversary ed. (book; no open edition)
- [welc] M. Feathers, *Working Effectively with Legacy Code* (book); companion practice — https://understandlegacycode.com/

Slug definitions, verification status, and per-section mapping: `references/source-map.md`.
