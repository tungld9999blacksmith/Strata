# ADR-04: Backend architecture (hybrid of modular monolith, clean architecture and vertical slices)

**Status:** Accepted (2026-11-03). Builds on ADR-03 (technology stack, [PRD section 9.3](../product/PRD_Strata_MVP.md)).

## Context

The backend is an ASP.NET 9+ modular monolith. It has to keep several business areas (work graph, evidence, gates, traceability and so on) independent in code, keep the domain rules testable without a database, and stay easy to change one use case at a time. No single architectural style covers all three needs, so the backend combines several styles.

## Decision

The backend is a **hybrid** of three architectural styles plus one modeling style. Each one answers a different question, so they are applied at different levels and do not compete with each other.

| Level | Style | Question it answers | Where it appears |
|---|---|---|---|
| System | **Modular monolith** | How is the system split into parts that can be developed and, later, deployed separately? | One folder per business module under `Modules/`; one deployable API and one Worker |
| Module | **Clean architecture** | Which code may depend on which? | Four projects per module (Domain, Application, Infrastructure, Api); ports in Application, adapters in Infrastructure |
| Use case | **Vertical slice** | How is code grouped inside a module? | `Application/Features/<UseCase>/`, with the command, handler, validator and response of one use case together |
| Shared concepts | **DDD shared kernel** | Which concepts are shared across modules? | `Shared/Strata.SharedKernel` and `Shared/Strata.Platform.Abstractions` |

The styles intersect as follows:

- **Modules cut the system horizontally by business capability.** Each module owns its data and its rules. Other modules see only its `Contracts` project or its integration events.
- **Clean architecture cuts each module into layers.** Dependencies point inward: Domain depends on nothing, Application depends on Domain and ports, and Infrastructure and Api depend on Application. Database, message queue, email and logging are infrastructure constraints. They are defined as ports and implemented as adapters, so the business rules never depend on them.
- **Vertical slices cut each layer by use case.** A single use case (for example "create item") spans Api, Application and Infrastructure, and all of its files sit together in one folder. A change to one use case does not touch the code of another.

A request flows through all three styles:

```text
HTTP request
  → Api: endpoint (maps HTTP to a command)                          [module, Api layer, slice]
  → Application: command → validator → handler (permission, rules)  [module, Application layer, slice]
  → Domain: aggregate enforces invariants                           [module, Domain layer]
  → Application port: IItemRepository, IProjectSequence             [module, port]
  → Infrastructure: EF Core adapter → PostgreSQL (RLS by tenant)    [module, Infrastructure layer]
  → Outbox: integration event for other modules                     [Platform, asynchronous]
```

## Rationale

- **Modules** keep the monolith's boundaries enforceable, so it can be split into services later (see [PLAN](../product/PLAN_Strata.md), extension points) without a rewrite.
- **Clean architecture** keeps the domain rules testable without a database and keeps the infrastructure replaceable.
- **Vertical slices** keep each use case small and independent, which makes changes local and reviews easier. They also avoid large shared service classes that grow with every feature.

## Consequences

- Every module needs five projects, so the solution has more projects to maintain. Scaffolding should generate them consistently.
- Architecture tests in CI are required to keep module boundaries and slice isolation from eroding.
- Some code is duplicated across slices by design. Shared logic moves to the Domain or behind a port only when at least two slices need the same rule.

## Open questions

- **CQRS:** the current slice shows only the write side (command and handler). A read side (query handlers and read models or projections for the feature profile) is not yet specified. See [README section 7.2.1](../../README.md) for the example slice.
