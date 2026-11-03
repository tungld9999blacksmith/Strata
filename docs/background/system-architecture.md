# Proposed system architecture: project management workspace (software · research · AI)

## Context

The product is a unified workspace for software projects, research and AI. From the brainstorm, the core architectural requirements are:

- A common data model: item, link, artifact, event; each project kind is only configuration.
- Gated workflows, with evidence collected automatically from Git/CI/test results, reproducible through snapshots.
- Feature profiles: timeline, change history, version tree, loops.
- Private/public personal spaces, projects, organizations; permissions down to the artifact level; multi-tenancy.
- AI agents as a type of actor: they act through a tool layer, write through changesets, and operate under guardrails.
- Range estimates, risk register, bottleneck detection.

The current working directory (finance-manager) contains no related code, so there is no existing code to reuse. This is a design from scratch. The side chats about RBAC/ABAC and multi-source synchronization have not been read; they should be read before the Permissions and Connectors sections are finalized.

## Architecture principles

1. **Modular monolith first, split services later.** One codebase, clear module boundaries; split only when load or isolation pressure appears (workflow engine, agent runtime, ingest).
2. **Relational state + immutable event log.** Relational tables hold the current state; every change writes an `event` (outbox) that serves as the source for the timeline, reports and projections.
3. **One policy decision point** shared by the UI, API, search and agents.
4. **All data access goes through one authenticated tool layer** (including agents); no shortcut to the database.
5. **Evidence is an immutable snapshot**; the gate evaluates only snapshots.
6. **Connectors are plug-in adapters** that normalize to a common schema.

## Logical diagram

```
Clients: Web app · (mobile/capture) · CLI/API · External agents (MCP)
              │
Edge: API gateway / BFF · OIDC auth · tenant resolver (subdomain→tenant) · rate limit
              │
┌─────────────┴────────────────────────────────────────────────────────┐
│ Modular monolith (a module = a boundary that can be split later)     │
│  identity+tenancy · spaces+policy · work graph (item/link/kind)     │
│  workflow engine (node/gate/token) · evidence (artifact/snapshot)    │
│  ideas · estimation+risk · flow analytics · notifications            │
│  search/retrieval (permission-aware) · agent tool gateway (MCP)      │
└─────────────┬────────────────────────────────────────────────────────┘
              │ outbox → job queue
Workers: connector ingest · gate evaluator · projection builders ·
         schedulers · agent runners (sandbox)
              │
Data: PostgreSQL (RLS, JSONB, FTS, pgvector) · object storage (artifacts/logs)
      · queue (initially PostgreSQL-based) · secrets vault · tenant control plane
```

## Main components

| Module | Responsibility | Design notes |
|---|---|---|
| **Identity + tenancy** | Users, organizations, SSO, `tenant` table (tenant → data location) | Domain names are for routing only; real isolation is at the data layer |
| **Spaces + policy** | Personal private/public spaces, projects, organizations; RBAC by role + ABAC by attributes (space, data classification, kind) | Publishing is an explicit action that creates a `publication` |
| **Work graph** | `item`, `link`, kind registry (field schema + lifecycle + template) | Common columns + `attrs` JSONB with a registered schema |
| **Workflow engine** | source/input/process/switch/gate/agent/output nodes; tokens; gate results pass/fail/needs_review | Gate rules are versioned data; durable execution (can be restarted) |
| **Evidence** | `artifact`, `source_snapshot`, AC↔test↔result traceability | Snapshots carry immutable identifiers (commit SHA, run ID) |
| **Event store + projections** | Immutable `event`; timeline, change history, version tree, flow metrics | Projections can be rebuilt from events |
| **Ideas** | Ideas, assumptions, results, private by default | Back-link when converted into an item |
| **Estimation + risk** | Range estimates, risk register, estimate history | Every estimate revision is stored |
| **Flow analytics** | Cycle time, WIP, CFD, bottleneck rules → suggestions | Rule-based first, measured at the process level |
| **Search/retrieval** | Full-text + vector, permission filtering at retrieval time | Each chunk carries its space and visibility level |
| **Agent tool gateway** | The app's own MCP server; changesets; agent identity; budget; sandbox | Agents do not write directly to core data |
| **Integrations** | GitHub/GitLab connectors, JUnit, CI; webhooks + reconciliation polling | Idempotent by `external_ref`; least-privilege tokens in the vault |

## Representative flows

1. **Commit/PR → gate.** Webhook → ingest → normalize → `source_snapshot` → link to `CHAT-27` → write event → gate evaluator reads the snapshot → move token → notify.
2. **Agent node.** Build the context package according to the input contract → team members review/edit → runner executes in a sandbox through the tool gateway → output goes to the draft area → changeset → gate `needs_review` → a person accepts → promote + write `agent_run`.
3. **Publish private → public.** Preview content → create a copy with a back-reference → `publication` + event → can be revoked.

## Data and multi-tenancy

- **PostgreSQL** is the main store. `tenant_id` on **every table** + Row-Level Security; this corrects the earlier schema, in which the workspace was only linked indirectly.
- **Hybrid model:** pooled for the standard plan; silo (dedicated DB, possibly per region) for enterprise/university customers; the `tenant` control plane maps data locations so that moving between them requires no application code change.
- **Object storage** for logs, reports and original files; the DB keeps only summaries and references.
- **Outbox pattern** so that events and state changes stay consistent; the queue starts on Postgres and moves to NATS/Kafka when needed.

## Security

- Central policy engine; permission decisions for every layer, including search and agents.
- An agent's effective permissions = the intersection of the delegating person's permissions and the agent's allowlist.
- Organization administrators **cannot** read private spaces (legal exceptions follow a separate, traceable process).
- Content created by others is always treated as untrusted data by agents (protection against prompt injection); code run by agents executes in a sandbox.
- Encryption at rest; per-tenant keys for the enterprise plan; audit trail for every permission change.
- Data classification decides which content may be sent to external models; self-hosted models are an option.

## Key decisions and recommendations

| Decision | Recommendation | Reason |
|---|---|---|
| Architecture | Modular monolith | Small team, many ideas not yet validated |
| Database | A single PostgreSQL at the start (JSONB, FTS, pgvector, RLS) | Fewer operational components |
| Workflow execution | A simple in-house engine + durable queue; consider Temporal when needed | Avoid heavy dependencies in the MVP |
| Agent tooling | Internal MCP server; external agents connect through the same interface | A single access path |
| Stack | ASP.NET 9+ backend, Angular 19+ frontend (standalone components) | Matches team skills; see PRD section 9.3 | Two languages to maintain (C# and TypeScript) |
| Build or base on open source | Build a thin custom core | The gate/evidence/agent model differs from existing PM platforms |

## Build roadmap by slice

1. **Core + tenancy + RLS + events** (work graph, item/link/artifact, basic roles).
2. **GitHub + JUnit connectors**, snapshots, feature profile and timeline.
3. **Workflow + gate** (source/gate/output nodes, needs_review, checkpoints).
4. **Agents at levels 0–1** through the tool gateway, changesets, `agent_run`.
5. **Private/public spaces + publishing**, permission-filtered search.
6. Range estimates, risks, bottleneck analysis; research/AI modules; silo DB.

## Architecture validation (spikes before committing)

- **Cross-tenant leaks:** automated tests prove that RLS blocks queries without `tenant_id`, including through vector search.
- **Idempotent webhooks:** duplicate or out-of-order deliveries of the same event still result in one snapshot.
- **Gate reproducibility:** re-running the gate on the same snapshot and rule version gives the same result.
- **End-to-end agent flow:** an agent node runs from context package to an accepted changeset, with a test that the agent cannot read another person's private space.
- **Rough load:** estimate events per day for a large tenant to determine when ingest and the queue must be split.

## Open questions

1. The first user group and kind (affects the slice order).
2. Whether agents are provided by the application or external; self-hosted models or an external service.
3. Whether "public" means within the organization or outside it.
4. Technology stack: decided (ASP.NET 9+ and Angular 19+, see PRD section 9.3).

## Note on pending work

One group of the deep research on scientific research is still in progress, and the research report writing step has not been done. The report writing step writes files, so it will be done after exiting planning mode.
