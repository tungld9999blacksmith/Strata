# ADR-04: Backend architecture (hybrid of modular monolith, clean architecture and vertical slices)

**Status:** Accepted (2026-11-03). Builds on ADR-03 (technology stack, [PRD section 9.3](../product/PRD_Strata_MVP.md)).

**Revision (2026-11-03):** each module is now **one class library** with five folders (`Domain`, `Interfaces`, `Common`, `Infrastructure`, `Features`). This replaces the earlier layout of several projects per module (Domain, Application, Infrastructure, Api, Contracts). Handlers depend on interfaces, never on `Infrastructure`.

## Context

The backend is an ASP.NET 9+ modular monolith. It has to keep several business areas (work graph, evidence, gates, traceability and so on) independent in code, keep the domain rules testable without a database, and stay easy to change one use case at a time. No single architectural style covers all three needs, so the backend combines several styles.

## Decision

The backend is a **hybrid** of three architectural styles plus one modeling style. Each one answers a different question, so they are applied at different levels and do not compete with each other.

| Level | Style | Question it answers | Where it appears |
|---|---|---|---|
| System | **Modular monolith** | How is the system split into parts that can be developed and, later, deployed separately? | One class library per business module under `Modules/`; one deployable API and no separate worker |
| Module | **Clean architecture** | Which code may depend on which? | Five folders per module (`Domain`, `Interfaces`, `Common`, `Infrastructure`, `Features`); ports and API interfaces in `Interfaces`, adapters in `Infrastructure` |
| Use case | **Vertical slice** | How is code grouped inside a module? | `Features/<UseCase>/`, with the command or query, validator, handler, response and endpoint file of one use case together |
| Shared concepts | **DDD shared kernel** | Which concepts are shared across modules? | `Shared/Strata.SharedKernel` and `Shared/Strata.Platform.Abstractions` |

The styles intersect as follows:

- **Modules cut the system horizontally by business capability.** Each module owns its data and its rules. Other modules see only its `Interfaces` folder (the public API interfaces and integration events).
- **Clean architecture cuts each module into folders.** Dependencies point inward: `Domain` depends on nothing, `Interfaces` depends on `Domain`, `Common` depends on `Domain` and `Interfaces`, `Infrastructure` and `Features` depend on `Domain`, `Interfaces` and `Common`. `Features` never depends on `Infrastructure`. Database, message queue, email and logging are infrastructure constraints. They are defined as interfaces and implemented as adapters, so the business rules never depend on them.
- **Vertical slices cut each module by use case.** A single use case (for example "create item") sits in one folder under `Features/`, with its command, validator, handler, response and endpoint file. A change to one use case does not touch the code of another.

A request flows through all three styles:

```text
HTTP request
  → Features: endpoint file (maps HTTP to a command)                  [module, slice]
  → Features: command → validator → handler (permission, rules)       [module, slice]
  → Domain: aggregate enforces invariants                             [module, Domain folder]
  → Interfaces port: IItemRepository, IProjectSequence                [module, port]
  → Infrastructure: EF Core adapter → PostgreSQL (RLS by tenant)      [module, Infrastructure folder]
  → Outbox: integration event for other modules                       [Platform, asynchronous]
```

## Module layout and dependency rules

| Folder | Holds | May depend on |
|---|---|---|
| `Domain` | Aggregates, value objects, domain events, domain services | `Strata.SharedKernel` only |
| `Interfaces` | API interfaces (the module's public contract), ports implemented by `Infrastructure`, integration events | `Domain`, shared kernel, platform abstractions |
| `Common` | Services and classes reused by several features | `Domain`, `Interfaces` |
| `Infrastructure` | EF Core mappings, repositories, adapters that implement the ports | `Domain`, `Interfaces`, `Common`, platform abstractions |
| `Features` | One folder per use case: command or query, validator, handler, response, endpoint file | `Domain`, `Interfaces`, `Common` |

- Because a module is one assembly, these rules are conventions that the compiler does not enforce by itself. Types in `Domain`, `Common`, `Infrastructure` and `Features` are `internal`; only `Interfaces` is `public`. Architecture tests in CI check the dependency table.
- Each module exposes `Add<Module>Module()` and `Map<Module>Endpoints()`, both in `<Module>Module.cs` at the root of the module project.

## Hosting and API style

- **One process, one entry point.** The backend runs as a single ASP.NET process, started from `Host/Strata.Api/Program.cs`, which is the only entry point. Modules are class libraries referenced by the host. The MVP runs one instance. This is a modular monolith, not microservices.
- **Background jobs in the same process.** The outbox dispatcher, pollers and job runners are hosted services registered in `Program.cs`. There is no separate worker deployment.
- **Minimal API only.** Each feature has one endpoint file that registers its route, and each module maps its routes through one `Map<Module>Endpoints()` extension. The backend does not use MVC controllers.

## Rationale

- **Modules** keep the monolith's boundaries enforceable, so it can be split into services later (see [PLAN](../product/PLAN_Strata.md), extension points) without a rewrite.
- **Clean architecture** keeps the domain rules testable without a database and keeps the infrastructure replaceable.
- **Vertical slices** keep each use case small and independent, which makes changes local and reviews easier. They also avoid large shared service classes that grow with every feature.
- **One project per module** keeps the solution small: modules only depend on their own folders, so splitting them into more projects adds maintenance without adding isolation.

## Consequences

- The solution has one project per module, which is easy to maintain. The cost is that folder dependencies are not enforced by project references, so `internal` visibility and architecture tests in CI are required to keep module boundaries and slice isolation from eroding.
- Some code is duplicated across slices by design. Shared logic moves to the `Domain`, to `Common`, or behind an interface in `Interfaces` only when at least two slices need the same rule.
- Background jobs share resources with the API process. Long-running work must be bounded so that it does not slow requests.
- Running more than one instance later would require job claiming with `SELECT … FOR UPDATE SKIP LOCKED`. This is out of scope for the MVP.

## Open questions

- **Module root file:** `Add<Module>Module()` and `Map<Module>Endpoints()` are placed in `<Module>Module.cs` at the module root, because they must reference several folders. This is an assumption to confirm, since the module is meant to contain only the five folders.
- **Request dispatch:** the handler pipeline (how a command reaches its validator and handler, and where shared behaviors such as validation and permission checks live) is not yet specified. It is expected to live in `Common`.
- **Read side:** query slices are defined, but read models or projections for the feature profile are not yet specified. See [README section 7.2.1](../../README.md) for the example slices.
