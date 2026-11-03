# PRD — Strata MVP (Workspace for software development projects)

> Strata is a workspace that connects work with evidence: each feature of a software project has one profile that gathers requirements, work items, commits/PRs, test results and decisions, updated automatically from GitHub and CI.
> **MVP scope:** software projects only. Research projects, scientific paper writing, AI/ML projects and AI agents are kept in the long-term plan ([PLAN_Strata.md](PLAN_Strata.md)) and Appendix B.
> **Canonical source:** architecture decisions are in [system-architecture.md](../background/system-architecture.md). Pain points that cannot yet be verified are labeled "Assumption" and must be validated through field interviews (section 14).

---

## 0. Document information

| Field | Value |
|---|---|
| Product | Strata — unified workspace for software projects, research and AI (MVP: software) |
| Version | v0.1 |
| Status | Draft — waiting for the first user group and build team size to be confirmed |
| Product Owner | TBD |
| Project Manager | TBD |
| Tech Lead | TBD |
| Last updated | 2026-11-03 |
| Timeframe | T0 → T0 + 10 weeks (T0 = the day the build team is fully staffed; not yet fixed) |

### Change log

| Version | Date | Change |
|---|---|---|
| v0.1 | 2026-11-03 | First version. Scope narrowed from "software · research · AI" to software. Research/AI kept as later phases (Appendix B). |

---

## 1. Summary

**Problem:** information about a software feature is scattered across the work management tool, Git, CI and documents. BA, QA and PO have to ask developers or compile information by hand to find out where a feature is, whether it has been verified, and why a requirement changed.

**MVP solution:** Strata gathers everything around a **feature**. Commits, PRs and test results (JUnit) arrive automatically from GitHub/CI and are stored as **immutable snapshots**. Each feature has a profile and a timeline. A simple **quality gate** evaluates the snapshot to decide whether the feature is "eligible for completion".

**MVP success:** a software team of 3–10 people uses Strata for real work for at least 4 weeks, most commit/PR–item links require no manual entry, and BA/QA can answer "where is feature X?" without asking developers.

```
GitHub/CI → webhook/upload → immutable snapshot → link to item → feature profile + timeline → gate → completion decision
```

**Terms:** *feature* is the user-facing unit of work, with its own profile. *F-n* (F1…F10) are the features of Strata itself as described in this document.

---

## 2. Problems (Pain points)

Evidence labels: **Sourced** = backed by a survey or article, mostly from tool vendors, so only directional value; **Assumption** = inferred from brainstorming, not yet validated through interviews.

### 2.1 From the makers' side (Dev, QA)

| ID | Pain point | Evidence | Features |
|---|---|---|---|
| PP-01 | Information about a feature is scattered across many tools and must be compiled by hand | Sourced (Atlassian survey 2025, directional) | F5, F6, F7 |
| PP-02 | Heavy processes with many formalities that add no value; leadership does not understand developers' difficulties | Sourced (Atlassian: 63% of developers; InfoWorld) | F3, F10 |
| PP-03 | Manual entry to "update status" even though the data already exists in Git/CI | Assumption | F5, F6 |
| PP-04 | Checking "good enough to release" relies on gut feeling and manual checks, hard to reproduce | Assumption | F6, F8 |

### 2.2 From the coordinators' side (PO, PM, BA)

| ID | Pain point | Evidence | Features |
|---|---|---|---|
| PP-05 | Without asking developers, the real status of a requirement compared with code/tests is unknown; BA/QA do not need to read code but need to know "has it been verified?" | Assumption | F7, F9, F10 |
| PP-06 | The history of why a requirement changed and which decisions were settled is lost | Assumption | F4, F7 |
| PP-07 | Tools are complex to configure and need a dedicated specialist ("a second Jira") | Sourced (opinion articles, directional) | F2, F3 |

---

## 3. Goals and non-goals

### 3.1 Goals

| # | Goal | Pain point |
|---|---|---|
| G1 | A feature has one single place for requirements, work items, commits/PRs, test results and decisions | PP-01, PP-06 |
| G2 | Evidence arrives automatically from GitHub and CI; manual entry is only an exception | PP-03 |
| G3 | BA/QA/PO know the verification status of a feature without reading code | PP-05 |
| G4 | The decision "eligible for completion" is based on a snapshot and is reproducible | PP-04 |
| G5 | Simple defaults: teams start using it in under 30 minutes, with no workflow configuration | PP-02, PP-07 |

### 3.2 Non-goals

Does not replace Jira/Linear in every case (no free-form field or workflow configuration); does not store or display source code; does not measure or rank individuals; the MVP has no AI agent, research, AI/ML, estimation or bottleneck analysis.

---

## 4. Users

| Persona | Description | Channel | Spec |
|---|---|---|---|
| Workspace Admin | Creates the workspace, invites members, connects GitHub | Web app | F1, F2, F5 |
| PO | Decides value and priorities, accepts features | Web app | F3, F7, F8, F10 |
| PM | Sprint progress, risks, gate configuration | Web app | F3, F8, F10 |
| BA | Writes requirements and acceptance criteria (AC), traces them | Web app | F3, F7, F9 |
| QA | Tests, bugs, gate approval | Web app | F6, F8, F9 |
| Dev | Works on items, links commits/PRs, views build/test results | Web app, GitHub | F3, F5, F6 |

One person may hold several roles (for example both PO and PM).

### Representative use cases

- **UC-A — "Where is KEY-5?"** A BA opens the KEY-5 profile → sees 3 AC, 2 AC with passing tests in the latest snapshot, 1 uncovered AC, and 1 PR under review.
- **UC-B — "Is it eligible for completion yet?"** A developer moves KEY-5 to `done` → the gate evaluates the snapshot → `needs_review` → QA approves → `done`, recording the approver and the snapshot.
- **UC-C — "Why did the requirement change?"** A PO opens the KEY-5 timeline → sees a comment marked *Decision* and the history of AC changes.

---

## 5. Scope

| Priority | ID | Feature | Milestone |
|---|---|---|---|
| Must | F1 | Workspace, tenancy and sign-in | M1 |
| Must | F2 | Roles and permissions | M1 |
| Must | F3 | Projects, items, links, sprints, bugs | M2 |
| Must | F4 | Comments, mentions, notifications | M2 |
| Must | F5 | GitHub connection | M3 |
| Must | F6 | JUnit test results and snapshots | M4 |
| Must | F7 | Feature profile and timeline | M4 |
| Should | F8 | Basic quality gate | M5 |
| Should | F9 | AC ↔ test ↔ result traceability | M5 |
| Could | F10 | Role-based views | M5 |

**Out of scope (MVP):** full workflow designer; AI agents; private/public spaces and publishing; research projects, paper lifecycle, hypotheses/experiments; AI/ML projects and experiment tracking; range estimation, risk register; bottleneck analysis and coaching; version tree, requirement history diffs; ABAC, artifact-level permissions; per-tenant DB (silo); GitLab, CI/SonarQube/Figma/Confluence connectors; mobile app. Details in Appendix B.

---

## 6. Feature Requirements

### F1 — Workspace, tenancy and sign-in

- OIDC sign-in (GitHub, Google). No passwords stored in the MVP.
- The workspace is the tenant unit. One person may belong to several workspaces; invitations by email or link valid for 7 days.
- Every table has `tenant_id` and Row-Level Security (RLS).

**Acceptance criteria**
- AC-F1-01: _Given_ a user without an account, _when_ they sign in with GitHub for the first time, _then_ an `app_user` is created and they are asked to create or join a workspace.
- AC-F1-02: _Given_ a member of workspace A, _when_ they call the API with the id of an item in workspace B, _then_ the response is 404 and the refused access is logged.
- AC-F1-03: _Given_ a query without the tenant condition (in the test suite), _when_ it runs under the application role, _then_ RLS returns 0 rows.
- AC-F1-04: _Given_ an invitation older than 7 days, _when_ the link is opened, _then_ it reports that the invitation has expired and no membership is created.

### F2 — Roles and permissions

- Roles: Workspace Admin, PO, PM, BA, QA, Dev, Viewer. Multiple roles may be assigned to one person, per project.
- Permission = action × object type, stored as data (`role`, `permission`, `role_permission`). The MVP uses RBAC only.
- Every role change writes an event.

**Acceptance criteria**
- AC-F2-01: _Given_ a person with both PO and PM roles, _when_ permissions are checked, _then_ the effective permissions are the union of both roles.
- AC-F2-02: _Given_ a Viewer, _when_ they call the API to create an item, _then_ the response is 403 and the UI does not show the create button.
- AC-F2-03: _Given_ an Admin changes a member's role, _when_ saved, _then_ a `role_assignment.changed` event is written with actor, before and after.
- AC-F2-04: _Given_ only one Admin remains, _when_ that Admin removes their own role, _then_ the change is refused.

### F3 — Projects, items, links, sprints, bugs

- A project has a `key` of 2–6 uppercase characters and a `kind` column (fixed to `software` in the MVP).
- Item types: `epic`, `story`, `task`, `bug`, `feature` (a feature is the anchor of the F7 profile). Default lifecycle: `backlog → in_progress → review → done`, plus `cancelled`.
- Links: `parent_of`, `blocks`, `relates_to`, `implements`, `tests`, `duplicates`.
- Backlog ranked by drag-and-drop, Kanban board, sprints (start/end dates, items assigned). Bugs have severity S1–S4.
- Item code `KEY-n`, incremented atomically. Soft deletion.

**Acceptance criteria**
- AC-F3-01: _Given_ two people create items at the same time, _when_ both are saved, _then_ they get two different codes, with no duplicate and no broken sequence.
- AC-F3-02: _Given_ an item in `backlog`, _when_ it is moved directly to `done`, _then_ the change is refused under the default lifecycle.
- AC-F3-03: _Given_ a bug is created, _when_ its severity is missing, _then_ it is refused with a required-field message.
- AC-F3-04: _Given_ an item is deleted, _when_ the deletion is confirmed, _then_ it is soft-deleted, the event is recorded, and a PM can restore it within 30 days.
- AC-F3-05: _Given_ backlog items are reordered by drag-and-drop, _when_ dropped, _then_ the order is saved and other people see it within ≤ 2 seconds.

### F4 — Comments, mentions, notifications

- Markdown comments, `@mention`, and marking a comment as a **Decision**.
- In-app notifications and summary emails; each person configures their own settings.

**Acceptance criteria**
- AC-F4-01: _Given_ a comment containing `@An`, _when_ it is posted, _then_ An receives a notification within ≤ 10 seconds; people without permission to view the item do not receive its content.
- AC-F4-02: _Given_ a comment is edited or deleted, _when_ saved, _then_ the change history is kept in events.
- AC-F4-03: _Given_ email is turned off, _when_ a mention occurs, _then_ it is still visible in the app and no email is sent.
- AC-F4-04: _Given_ a comment is marked as a Decision, _when_ saved, _then_ it appears in the profile's Decisions section and on the timeline.

### F5 — GitHub connection

- Install a **GitHub App** with minimum permissions: read metadata, PRs, checks, workflow runs. Strata does not read source file contents.
- Receives the webhooks `push`, `pull_request`, `check_run`, `workflow_run`. Verifies the HMAC signature. Processes idempotently by delivery id and `external_ref`.
- Reconciliation polling every 15 minutes to compensate for missing webhooks.
- Automatic linking by item code (`KEY-12`) in the branch name, commit message and PR title. Manual linking and unlinking are allowed; the origin of each link is stored (`auto` / `manual`).
- Artifacts: `commit`, `pull_request`, `ci_run`.

**Acceptance criteria**
- AC-F5-01: _Given_ a push of a commit "KEY-12 fix sign-in", _when_ the webhook arrives, _then_ a `commit` artifact linked to KEY-12 is created and appears on the timeline.
- AC-F5-02: _Given_ a webhook with the same delivery id is sent 3 times, _when_ processed, _then_ only one artifact and one event exist.
- AC-F5-03: _Given_ a webhook with a wrong signature, _when_ received, _then_ the response is 401 and it is not processed.
- AC-F5-04: _Given_ a webhook was lost, _when_ polling runs, _then_ the missing artifact is created within ≤ 15 minutes with source `poll`.
- AC-F5-05: _Given_ an automatic link is wrong, _when_ a person with permission removes it, _then_ the link is removed, the event is recorded, and the link is not automatically recreated for the same pair.

### F6 — JUnit test results and snapshots

- Upload JUnit XML through the API (project CI token) or a sample GitHub Action. Parses `testsuite`/`testcase`: pass, fail, skip, duration, error message.
- Each upload creates an immutable `source_snapshot`: commit SHA, run id, file hash. The original file is stored in object storage; the DB stores a summary and a reference.
- Linked to items through commit SHA, PR and branch.

**Acceptance criteria**
- AC-F6-01: _Given_ a valid JUnit file, _when_ uploaded, _then_ a snapshot is created whose pass/fail/skip totals match the file and which is tied to the commit SHA.
- AC-F6-02: _Given_ the same (SHA, run id, hash) is uploaded again, _when_ processed, _then_ no second snapshot is created.
- AC-F6-03: _Given_ broken XML, _when_ uploaded, _then_ the response is 422 with the reason, and no snapshot is created.
- AC-F6-04: _Given_ a file larger than 20 MB, _when_ uploaded, _then_ the response is 413.
- AC-F6-05: _Given_ a snapshot has been created, _when_ an API call tries to edit or delete it, _then_ no endpoint allows it.

### F7 — Feature profile and timeline

- The profile gathers: description/requirements, AC, child items, commits/PRs (metadata and links only, no diffs), latest test results, decisions, and timeline.
- The timeline is the event stream filtered by the feature and its descendants, sorted by time, and filterable by type (status, code, test, comments, decisions).
- Displayed by action and role, not by individual ranking.

**Acceptance criteria**
- AC-F7-01: _Given_ a feature has 500 events, _when_ its profile is opened, _then_ the page loads within ≤ 2 seconds (p95) and shows child items, PRs and the latest test results.
- AC-F7-02: _Given_ a new linked commit, _when_ the webhook has been processed, _then_ it appears on the timeline within ≤ 30 seconds.
- AC-F7-03: _Given_ a BA opens a profile, _when_ they view commits/PRs, _then_ they see only the title, status, author and GitHub link; no diff or source code.
- AC-F7-04: _Given_ the timeline is filtered by "test", _when_ applied, _then_ only the feature's test events are shown.

### F8 — Basic quality gate

- The gate is attached to the `review → done` transition of a feature, or to a release. There is no workflow designer.
- Rules are **versioned data**, for example `tests.failed == 0`, `tests.passed_ratio >= 0.95`, `bugs.open(severity<=S2) == 0`, `approved_by has role QA`.
- Results: `pass`, `fail`, `needs_review`. The gate evaluates the latest snapshot. Each run stores an immutable `gate_run` with the snapshot and the rule version.
- The requester may not approve their own gate.

**Acceptance criteria**
- AC-F8-01: _Given_ a snapshot has 2 failing tests and the rule `failed == 0`, _when_ the gate runs, _then_ the result is `fail`, the failing tests are listed, and the feature does not move to `done`.
- AC-F8-02: _Given_ a rule requires QA approval and the tests pass, _when_ the gate runs, _then_ the result is `needs_review`; once QA approves, it becomes `pass` and the approver is recorded.
- AC-F8-03: _Given_ the same snapshot and rule version, _when_ the gate is run again, _then_ the result is the same.
- AC-F8-04: _Given_ a rule is edited and saved, _when_ saved, _then_ a new version is created; old `gate_run` records keep the old rule version.
- AC-F8-05: _Given_ the requester moves an item to `done`, _when_ they try to approve it themselves, _then_ it is refused.

### F9 — AC ↔ test ↔ result traceability

- AC have stable IDs (`KEY-5/AC-3`). Tests map to AC by a tag in the test name or classname (for example `AC:KEY-5/AC-3`), or by manual linking.
- Matrix of AC × tests × latest snapshot results; AC coverage.
- Changing the content of an AC marks its test links as `stale` until QA confirms.

**Acceptance criteria**
- AC-F9-01: _Given_ a test tagged `AC:KEY-5/AC-3` passes in the latest snapshot, _when_ the matrix is opened, _then_ AC-3 is "verified" with a reference to the snapshot.
- AC-F9-02: _Given_ an AC has no test, _when_ the matrix is opened, _then_ it shows "uncovered"; coverage = AC with tests / total AC.
- AC-F9-03: _Given_ the content of AC-3 is edited, _when_ saved, _then_ AC-3's test links are `stale` until QA confirms.

### F10 — Role-based views

- Role-based home page: PO (prioritized backlog, features awaiting acceptance), PM (sprints, blocked items), BA (requirements missing AC), QA (gates awaiting review, open bugs), Dev (my work, PR/CI status).
- BA/QA hide code information by default.

**Acceptance criteria**
- AC-F10-01: _Given_ QA opens the home page, _when_ a `needs_review` gate is waiting for QA, _then_ the list appears at the top.
- AC-F10-02: _Given_ a Dev opens the home page, _when_ items are assigned to them, _then_ they are shown with the latest PR and CI status.

---

## 7. Data and evidence principles

| Principle | Rule |
|---|---|
| Immutable evidence | Snapshots carry the commit SHA and run id; not edited, not deleted. The gate evaluates snapshots, not the "current branch". |
| Append-only events | Every change (status, permissions, gate rules) writes an event; there is no API to edit or delete events. |
| Automatic data comes first | Automatic sources (Git, CI) are preferred. Manual entry is an exception and its origin is recorded. |
| Measure the process, not people | No individual rankings; the timeline is not used for scoring. |
| Gate is a proposal with human approval | A passing gate does not guarantee correct quality; rules require human approval for important changes. |
| Minimum permissions | GitHub tokens are read-only; BA/QA do not need to see code; Strata does not store source code. |
| AI | The MVP has no AI agent. When added (Appendix B), an agent proposes and the gate decides; see [PLAN_Strata.md](PLAN_Strata.md). |

---

## 8. Non-functional requirements

The numbers are **proposals** and must be confirmed with real load data.

| Area | Requirement |
|---|---|
| Performance | Read API p95 ≤ 300 ms; board with 200 items loads in ≤ 1.5 s; feature profile with 500 events ≤ 2 s (p95); webhook → timeline ≤ 30 s. |
| Data isolation | `tenant_id` on every table + RLS; the automated test suite proves that queries without a tenant return 0 rows, including through search. |
| Security | OIDC; server-side permission checks for every API; integration tokens encrypted at rest; HMAC signature on webhooks; OWASP Top 10 compliance; audit trail of permission changes. |
| Reliability | Idempotent webhooks and uploads; reconciliation polling; daily backups; RPO ≤ 24 hours, RTO ≤ 4 hours. |
| Observability | Structured logs; trace id across webhook → worker → timeline; ingest latency metrics. |
| Extensibility | Register `kind` and item types as data; `attrs` JSONB with a registered schema; modular monolith with clear module boundaries. |
| Privacy | No per-person metrics collected for ranking; workspace data can be exported and deleted on request. |
| Availability | Web, desktop-first, usable on screens ≥ 360 px; Vietnamese and English. |

---

## 9. Architecture and technology

| Component | Choice | Role |
|---|---|---|
| Web app | Angular 19+ SPA, standalone components | Board, feature profile, timeline |
| API | ASP.NET 9 modular monolith (vertical slices, clean architecture) | Business logic, permission checks |
| Database | PostgreSQL (RLS, JSONB, full-text) | Core data, events |
| Object storage | S3-compatible | Original JUnit files, logs |
| Queue | Job table in PostgreSQL (initially) | Webhook ingest, polling, gate |
| Worker | Separate ASP.NET 9 host in the same solution | Webhook handling, JUnit parsing, gate evaluation |
| Authentication | OIDC (GitHub, Google) | Sign-in |
| Integration | GitHub App | Webhook + reconciliation polling |

The stack is decided in section 9.3 (ADR-03). The detailed C4 diagrams are in [README.md](../../README.md#5-c4-architecture).

### 9.1 Decision: item data model (ADR-01)

**Status:** Proposed. **Context:** items have common columns and type/kind-specific fields; the MVP only needs `software`, but later phases add research and AI.

| Criterion | A. Common columns + `attrs` JSONB with registered schema | B. Separate table per kind |
|---|---|---|
| Adding a new kind | Register a schema, no migration | New migration and code |
| Querying across kinds | One table | Several tables, union |
| Data validation | Schema checks needed in the application layer | Strong DB constraints |
| Filter performance | Expression indexes when needed | Better on dedicated columns |

**Proposed decision:** A. Common columns (`id`, `tenant_id`, `project_id`, `type`, `state`, `title`, `seq`…) and `attrs` JSONB; fields that are often filtered are promoted to columns once measurements show the need.

### 9.2 Decision: tenant isolation (ADR-02)

**Status:** Proposed. Pooled with `tenant_id` + RLS for the MVP; a `tenant` table acts as the routing layer (tenant → data location), so large customers can later move to a dedicated DB without changing application code. Domain names are used for routing only, not as a security mechanism.

### 9.3 Decision: technology stack (ADR-03)

**Status:** Decided (2026-11-03). Resolves PQ-05.

- **Backend:** ASP.NET 9+ (C#). One solution containing the API host and the worker host.
- **Frontend:** Angular 19+ with standalone components, and no NgModules.
- **Architecture:** modular monolith. Each module is organized as a vertical slice (one folder per use case) inside Clean Architecture layers (Domain, Application, Infrastructure, Api). Infrastructure constraints (database, message queue, email, logging) are defined as ports and implemented in adapters. A shared kernel (DDD style) holds only generic building blocks.
- **Module boundaries:** modules communicate only through their public contracts or through integration events delivered via the outbox. Boundaries are enforced by architecture tests in CI.
- **Frontend structure:** `shared/` (reusable UI, directives, guards, interceptors, pipes, validators, services), `layout/` (header, footer, navbar, sidebar, shell) and `features/` (one lazy-loaded folder per feature slice).

**Why:** the team's skills (ASP.NET and Angular) and a strict module structure that keeps the modular monolith extractable later. The detailed structure is in [README.md](../../README.md#7-planned-repository-structure).

---

## 10. Success metrics

Targets are **proposals** without a baseline yet.

| Area | Metric | Target | How measured |
|---|---|---|---|
| Value | Share of commits/PRs automatically linked to items | ≥ 80% | Count of `auto` links / total commits in the pilot teams |
| Value | Time to answer "where is feature X?" | ≤ 1 minute | Guided observation with BA/QA, compared with the interview baseline |
| Adoption | Number of teams using it for real work | ≥ 3 teams | Weekly activity over 4 weeks |
| Adoption | Weekly active members | ≥ 60% | Sessions with at least one action |
| Safety | Cross-tenant leaks | 0 | RLS test suite in CI |
| Reliability | Duplicate or out-of-order webhooks creating copies | 0 | Automated tests + monitoring |
| Gate | Number of teams using the gate for real releases | ≥ 2 teams | Number of `gate_run` linked to releases |
| Performance | Feature profile with 500 events | ≤ 2 s (p95) | APM |

---

## 11. Release plan

Timing is counted in weeks from T0. The week counts assume a build team of 3–4 people and must be confirmed (PQ-01).

| Milestone | Timing | Content | Done when |
|---|---|---|---|
| M0 — Discovery | In parallel, 4–6 weeks | Interview 8–10 people per group; spikes on RLS, webhooks, gate | First group chosen; AC-F1-03 and AC-F5-02 pass on the spikes |
| M1 — Core | Weeks 1–2 | F1, F2, event store | AC-F1, AC-F2 pass |
| M2 — Work | Weeks 3–4 | F3, F4 | AC-F3, AC-F4 pass; usable internally |
| M3 — GitHub | Weeks 5–6 | F5 | AC-F5 passes on a real repo |
| M4 — Tests and profile | Weeks 7–8 | F6, F7 | AC-F6, AC-F7 pass; end-to-end demo of one feature |
| M5 — Gate and dogfooding | Weeks 9–10 | F8, F9, F10 (Could) | AC-F8, AC-F9 pass; ≥ 1 pilot team starts |

---

## 12. Risks

| Risk | Level | Mitigation |
|---|---|---|
| Scope spreads to research/AI before the MVP proves its value | High | Keep Appendix B; open later phases only after meeting the MVP exit criteria ([PLAN](PLAN_Strata.md)) |
| Most data does not arrive automatically (missing item codes in commits) → repeats the failure of "nobody records anything" | High | Suggest branch-naming conventions; one-step manual linking; measure the `auto` rate |
| Competition: Jira, Linear, GitHub Projects, Plane, OpenProject may add similar features | High | Focus on immutable snapshots + gate + traceability; validate value early through interviews (competition not yet verified) |
| Pain points not yet validated (many "Assumption" labels) | High | M0: interview before committing; adjust the PRD |
| Becomes "a second Jira" because of complex configuration | Medium | Good defaults; fixed workflow in the MVP; no custom fields |
| Gate is Goodharted (passing the gate ≠ correct quality) | Medium | Rules require human approval; show the gate reasons; never use it to score individuals |
| Cross-tenant leaks | High | RLS + automated tests in CI; review raw queries |
| Timeline becomes a personal monitoring tool | Medium | Display by action/role; no leaderboards |

---

## 13. Dependencies and assumptions

**Dependencies:** the GitHub App and webhooks work reliably; pilot teams use GitHub and have CI that generates JUnit XML; there is ASP.NET (C#) and Angular staff, and PostgreSQL experience; container infrastructure and object storage are available.

**Assumptions:** the target group is 3–10 people who use GitHub; the item-code convention in commits/PRs is acceptable; JUnit XML is common enough for the early stage (pytest, Go test and Jest can all export it); the build team is 3–4 people; there is no baseline data yet on the time spent compiling information.

---

## 14. Open questions

| ID | Question | Proposal |
|---|---|---|
| PQ-01 | Size and skills of the build team; T0 date? | Confirm before committing to the schedule in section 11 |
| PQ-02 | First user group: small product team, outsourcing, or student classes/projects? | Pick a group that already has CI + JUnit and 1 BA/QA |
| PQ-03 | GitHub only, or add GitLab in the MVP? | GitHub only; GitLab in phase 2 |
| PQ-04 | Build from scratch or on top of Plane/OpenProject? | Build a thin custom core; re-evaluate after M0 |
| PQ-05 | Technology stack? | Resolved: ASP.NET 9+ and Angular 19+ (section 9.3) |
| PQ-06 | SaaS or self-hosted? | SaaS first; self-host when a customer requests it |
| PQ-07 | Business model (commercial, internal, academic)? | Not decided; affects the silo/self-host decision |
| PQ-08 | Is an iteration a sprint or a cycle through the gate? | MVP uses sprints; gate cycles considered in phase 2 |
| PQ-09 | Is "Strata" an internal codename or a commercial name? Trademark and domain checks are needed before publishing | Use as a codename; check the trademark before launch |
| PQ-10 | Retention policy for test data (how long to keep the original JUnit files)? | 12 months, configurable per workspace |

---

## 15. Traceability

| PRD section | Source document |
|---|---|
| 2 Problems | `docs/background/brainstorm-summary.md` sections 2–3 |
| 3, 5 Goals, scope | `docs/background/brainstorm-summary.md` section 12 (P0 feature list), 14 (roadmap) |
| 6 F1, F2 | `docs/background/brainstorm-summary.md` sections 4.4–6; `docs/background/system-architecture.md` section on data and multi-tenancy |
| 6 F3 | `docs/background/brainstorm-summary.md` section 4 |
| 6 F5, F6 | `docs/background/brainstorm-summary.md` section 7.3 |
| 6 F7 | `docs/background/brainstorm-summary.md` section 8 |
| 6 F8, F9 | `docs/background/brainstorm-summary.md` sections 7.2, 7.4 |
| 9 Architecture | `docs/background/system-architecture.md` |
| Appendix B | `docs/background/brainstorm-summary.md` sections 11, 12 (P1, P2); `docs/background/research-landscape-report.md` |
| Long-term plan | [PLAN_Strata.md](PLAN_Strata.md) |

---

## Appendix A — Changes compared with the broader brainstorm

### A.1 Pain points

| Old pain point | Fixed |
|---|---|
| Pain points for software, research and AI/ML were combined | The MVP keeps only software pain points (section 2). Research/AI pain points moved to the Plan and need interviews before use. |
| Figures from vendor surveys treated as facts | Labeled "Sourced (directional)" or "Assumption". |
| No direct evidence for research PMs and AI team leads | Stated explicitly as a gap; interviews belong to the Discovery phase of the Plan. |

### A.2 Product and technical

| # | Brainstorm version | Current MVP | Reason |
|---|---|---|---|
| 1 | Three kinds: software, research, ai | Only `software`; the `kind` column is kept | A narrow slice with automatically available data |
| 2 | Full workflow designer (source/input/process/switch/gate/agent/output) | Basic gate attached to status transitions/releases | Avoid becoming "a second Jira" |
| 3 | AI agent levels 0–3 | No agent | Wait for gate and quality measurement; Plan phase 3 |
| 4 | Private/public spaces + publishing | Not yet | Needed for research and agents |
| 5 | Range estimates, risks, bottlenecks | Not yet | Requires historical data from real use |
| 6 | Permission-filtered search (FTS + vector) | Search by code and title | Vector/RAG requires spaces and data classification |

---

## Appendix B — Feature backlog for later phases

The order and entry/exit conditions are in [PLAN_Strata.md](PLAN_Strata.md).

| ID | Feature | MVP status | Planned scope | Technical notes |
|---|---|---|---|---|
| WF-01 | Workflow designer: source/input/process/switch/gate/output nodes | Fixed gate (F8) | Phase 2 | Tokens + dataflow; gate rules are versioned data; loops with limits |
| WF-02 | Propagation of `stale` when upstream changes | Only `stale` on AC (F9) | Phase 2 | Dependency graph between nodes |
| HIS-01 | Change history with diffs, version tree | Not yet | Phase 2 | `parent_version_id` for artifacts |
| INT-01 | Connectors for GitLab, CI (Jenkins, advanced GitHub Actions), SonarQube, Figma, Confluence | GitHub + JUnit only | Phase 2 | Adapters normalizing to the snapshot schema |
| AGT-01 | `agent` node, input contract, output schema, draft area, `agent_run` | None | Phase 3 | Through an MCP tool gateway; agents do not write directly to core data |
| AGT-02 | Guardrails: tool allowlist, budget, sandbox, emergency stop | None | Phase 3 | Against prompt injection: external content is data |
| AGT-03 | Autonomy levels 2–3, agent quality metrics | None | After phase 3 | Opened only when acceptance rates are stable |
| SPC-01 | Personal, project and organization private/public spaces; explicit publishing | Project default | Phase 3 | `publication` + back reference; revocable |
| SRC-01 | FTS + vector search with permission filtering | Code/title search | Phase 3 | Each chunk carries space and visibility level; pgvector |
| AUTHZ-01 | ABAC, artifact-level permissions | Project-based RBAC | Phase 3 | Shared policy decision point for UI/API/search/agent |
| EST-01 | Range estimates, spikes, risk register | Not yet | Phase 4 | Monte Carlo over historical throughput |
| FLOW-01 | Cycle time, WIP, CFD, bottleneck detection rules | Not yet | Phase 4 | Measures the process, not individuals |
| COACH-01 | Practice suggestions with evidence, PDCA, Andon | Not yet | Phase 7 | Suggestions are proposals; teams may decline |
| RSC-01 | `research` kind: hypotheses, experiments, results, status lifecycle | Not yet | Phase 5 | After interviewing research PMs |
| RSC-02 | Tracing claims in a manuscript ↔ original run/experiment | Not yet | Phase 5 | Chain: hypothesis → experiment → figures → manuscript |
| RSC-03 | Links to Zotero, Overleaf, OSF, ORCID/Crossref | Not yet | Phase 5 | Adapters; citation verification |
| RSC-04 | Paper writing lifecycle: drafts, peer review, submission, publication | Not yet | Phase 5 | Depends on publisher AI policies |
| ML-01 | `ai` kind: experiment runs, parameters, metrics, dataset versions, checkpoints | Not yet | Phase 6 | Record cancelled experiments with the reason |
| ML-02 | Connectors for MLflow, Weights & Biases, DVC; run comparison, basic model registry | Not yet | Phase 6 | Adapters; does not replace tracking tools |
| ENT-01 | Per-tenant DB (silo), data regions, tenant-based keys | Pooled + RLS | Phase 6 | The `tenant` routing table already exists from the MVP |
| MOB-01 | Mobile application | Responsive web | Not scheduled | Based on user needs |
