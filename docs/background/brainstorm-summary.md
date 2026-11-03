# Brainstorm summary: Project management application (Software · Research · AI)

> This document summarizes the whole discussion in this session. Assessments of existing products (Jira, Backlog, Notion, OSF...) and their internal architecture are based on general knowledge, **not yet verified against official documentation**. Pain point figures come from the sources listed at the end of this document, mostly surveys by tool vendors, so they only have directional value.

---

## 1. Vision

A unified project management platform for **software projects, research projects and AI/ML projects**, built around these ideas:

1. **A common core, configured per project kind:** the same data model (item, link, artifact, event); each project kind differs only in field schema, lifecycle, templates and integrations.
2. **Workflow with approval gates:** a project is a graph of source nodes, user nodes, switches, gates and output/checkpoints. A checkpoint is reached only after passing the gates.
3. **The feature is the main navigation unit**, not the task or role: each feature has a "profile" with a timeline, a version tree, loops and a change history.
4. **Evidence and history are first-class citizens:** every change is an immutable event; every result is tied to a snapshot of its data source.
5. **Make uncertainty visible:** range estimates, risk register, a "cannot estimate yet" state, re-forecasting in each cycle.
6. **The application is a data-driven observer, not a director:** it detects bottlenecks and suggests practices backed by evidence; teams decide.
7. **The AI agent is a kind of performer for a node:** the agent proposes, the gate decides; team members control the agent's input and output (see section 11).

---

## 2. Pain points found

### 2.1. By field

| Field | Main pain point | Confidence level |
|---|---|---|
| **Software** | Leadership does not understand developers' difficulties (63% of developers say so, up from 44%); heavy processes; unrealistic deadlines, many meetings, mid-course changes | Large Atlassian survey; some points are only opinion articles |
| **Research** | Dissatisfaction with ELNs; enterprise PM tools do not fit academia; lab notebooks are not maintained; paper notebooks are still used in parallel with digital records | Small surveys, posters, theses; no large study |
| **AI/ML** | Experiments abandoned (40–60% according to Comet); manual tracking (58% of teams); notebooks cause problems throughout; reproduction and collaboration are difficult | Mostly from Comet (a vendor), small sample (~508 US respondents) |

### 2.2. Common pain points

- Information is scattered and must be compiled by hand.
- Loss of history for decisions and experiments, making reproduction difficult.
- Tools do not match actual processes.
- A gap between managers and the people doing the work.
- Failures (cancelled experiments, rejected hypotheses) are not recorded as valuable data.

### 2.3. Evidence gap

No source has directly measured the pain points of **research project managers** or **AI team leads**. Field interviews are needed (see section 14).

---

## 3. Current state of products on the market

| Group | Examples | Solves well | Still lacking |
|---|---|---|---|
| General workspaces | Notion, Obsidian, Asana, ClickUp, Trello | Flexible, collaborative | No scientific structure, no evidence |
| Documents and literature reviews | Zotero, Mendeley, Elicit, ResearchRabbit | Citation management, paper discovery | Separated from work and experiments |
| ELN | Benchling, LabArchives, RSpace, SciNote, eLabFTW | Experiment records, audit trail | Weak on tasks and timelines; poorly received UX |
| Open platforms | OSF, Overleaf, OpenProject, Plane | Sharing, pre-registration, collaborative writing | OSF lacks work management |
| Institutional administration | Pure, Converis, InfoEd, Cayuse | Publication records, grants | Not built for researchers' daily work |
| ML tracking | MLflow, W&B, Comet, DVC | Runs, metrics, model registry | Unaware of hypotheses, documents, tasks |

**Common strengths of the products that solve problems well:** centralized storage, templates, versioning, integrations, sharing/permissions, reporting, search.
**Common weaknesses:** explaining *why* (decisions, reasons for cancellation), cross-domain linking, motivation to record, recording failures.

### The gap in the scientific paper lifecycle

No tool manages the full lifecycle. The chain *hypothesis → experiment → number in the paper → review → publication* is broken across OSF, ELNs, W&B, Overleaf, journal submission systems and CRIS. Tracing a claim in a manuscript back to the original run/experiment is still manual.

---

## 4. Core architecture

### 4.1. Five concepts

| Concept | Meaning |
|---|---|
| **Workspace** | Organization/team, the tenant unit |
| **Project** | A project with a `kind` (software / research / ai) |
| **Item** | A typed unit of work: task, bug, story, epic, hypothesis, experiment, run, paper... |
| **Link** | A typed relationship between two items: `blocks`, `implements`, `tests`, `cites`, `produced_by`... |
| **Artifact** | Evidence: commit, PR, build, file, dataset, checkpoint, PDF |
| **Event** | Immutable log: who changed what, when, with what result |

### 4.2. Kind is configuration

Each kind consists of: a field schema, a lifecycle (state machine), default templates/views, and default integrations.

- Software: `backlog → in progress → review → done`
- Research: `proposed → approved → running → analyzing → written → submitted`
- AI run: `queued → running → evaluated → promoted / discarded`

### 4.3. Data model (PostgreSQL, hybrid approach)

Fixed common columns + `attrs JSONB` for kind-specific parts. Main tables: `workspace`, `app_user`, `workspace_member`, `project`, `project_member`, `sprint`, `item`, `link`, `comment`, `artifact`, `item_artifact`, `release`, `release_item`, `event`.

Design notes:
- Item code (for example `CHAT-27`) = `project.key` + `item.seq` (incremented atomically).
- Soft deletion (`deleted_at`) to keep history.
- `event` is append-only; never edited or deleted.
- **To fix:** `project_member.role` is currently a single value; it must become many-to-many role assignment (see section 6).
- **To fix:** add `tenant_id` directly to **every table** if the pooled model is chosen (see section 5).

### 4.4. Multi-tenancy

| Model | Suitable for |
|---|---|
| Pooled (`tenant_id` + Row-Level Security) | Standard plan, small teams |
| Separate schema | Intermediate |
| Separate DB (silo) | Enterprises, universities, sensitive data, data-region requirements |

Proposal: a **hybrid model** with a `tenant` table as the routing layer (tenant → data location). Domain names (`org.app.com`) are used only for **routing**, not as a security mechanism; real isolation must be at the data layer.

---

## 5. Permissions

Lessons from Jira and Backlog (general knowledge):

- **Jira:** many layers (global → group/application → project role → permission scheme → issue security → workflow/field). Very detailed, complex to configure.
- **Backlog:** two levels (organization, project) with Administrator/Member/Reporter/Viewer roles. Simple, little detail at the issue level.

Applied to the product:
- Permissions down to the **artifact level** (unpublished data, sensitive data).
- Roles tied to the **kind** and to the **gate**.
- Separate rights to view / comment / edit / approve.
- Every permission change is written to the event log.
- Simple defaults; add complex layers when needed.

---

## 6. Roles and role-based functions

A role is a **set of permissions that can be assigned multiple times** (one person may be both PO and PM).

| Role | Responsibility | Gate approval rights | Main tools |
|---|---|---|---|
| **PO** | Value, priorities, acceptance, release | Business acceptance, release | Prioritized backlog, roadmap, AC acceptance table, value dashboard |
| **PM** | Schedule, scope, risks, resources | Plan, scope changes | Timeline/Gantt, risk register, capacity, automatic reports, change requests, workflow/gate configuration |
| **BA** | Requirements, acceptance criteria, traceability | Requirements sufficient for development | Requirements with IDs, traceability matrix, impact analysis, glossary |
| **QA** | Plans/test cases/results/defects | Quality, test readiness | Test case repository, CI result intake, defect lifecycle, AC coverage, release readiness report |
| **Dev** | Implementation, estimates, code linking | Does not approve business gates | Personal work queue, automatic commit/PR linking, build/test status, ADRs |

Principle: BA and QA **do not need to see source code**. The business layer uses *deliverables* and *acceptance criteria*; code/artifacts are the evidence behind them, hidden by default.

---

## 7. Workflow, gates and data sources

### 7.1. Model

A project = a graph of:
- **Entry:** data source, user input, or a deliverable created by a person.
- **Switch:** branches based on conditions/status.
- **Gate:** quality check.
- **Output/Checkpoint:** the final accepted point, with a snapshot.

### 7.2. Architectural decisions

- Combine **dataflow** (data flows through the pipeline) and **tokens** (the unit of work moving between states); a token carries a reference to a data snapshot.
- A gate has three outcomes: `pass` / `fail` (return to the previous node with a reason) / `needs_review` (hand over to a reviewer).
- Gate rules are stored as **structured data**, not arbitrary code. Examples: `failed == 0`, `coverage >= 0.80`, `approved_by has role QA`.
- Each gate result is an immutable event (which inputs, which rule version, who approved).

### 7.3. Data sources

Git (commits, PRs), CI, test results (JUnit XML, pytest, Go test JSON...), static analysis, work tracking tools, Figma/Confluence for design and requirements.

Principles:
- Sources create **snapshots** that carry immutable identifiers (commit SHA, run ID); the gate evaluates snapshots, not the "current branch".
- **Adapters** normalize every source to a common schema; a gate written once can be used across many sources.
- Idempotent processing, reconciliation by polling, least-privilege tokens, summaries stored in the DB while the original file is an artifact.
- Commit ↔ item linking through the code in the branch name/commit, with manual linking allowed and the origin of each link recorded.

### 7.4. Business traceability

```
Requirement (BA) → AC-03 → Test case (QA) TC-17 → CI result (PASS in ci-8817) → QA gate → AC-03 verified
```

Each AC has a stable ID; changing a requirement marks the related tests and gates for review (change propagation).

### 7.5. Problems to design for upfront

Loops when a gate fails (with a limited number of attempts), stale propagation when upstream changes, Goodhart risk (passing the gate ≠ correct quality, so human approval is needed), reproducibility (snapshot inputs/configuration/code), separation between the creator and the approver.

---

## 8. Feature dossier

A feature is the central navigation unit, gathering: requirements (several versions), design, development work, tests and results, defects, decisions.

| View | Answers | Data source | Difficulty |
|---|---|---|---|
| **Timeline** | What happened, in chronological order, who did it | Events filtered by feature | Low |
| **Change history** | What content changed (with diffs) | Versions of requirements/designs | Medium |
| **Version tree** | How versions inherit or branch | Artifacts with `parent_version_id` | Medium |
| **Spiral / loops** | How many cycles, what changed in each cycle and why | Events grouped by iteration + gate results | High |

Note: only create version trees for meaningful artifacts (requirements, designs, PRs, test cases); generate versions only when a gate, an approval or a marked change occurs; show the spiral as a secondary mode, with the cycle list and summaries as the default; default display by action/role, not by individual.

Open question: does an iteration map to a **sprint** or to a **cycle through a quality gate**? (Leaning toward the gate because it is tied to evidence.)

---

## 9. Estimation and uncertainty management

### 9.1. Limitations of current tools

A single number; false precision (Gantt charts down to the day); not linked to assumptions or risks; estimates used to evaluate teams (Goodhart); estimate history not kept; exploratory work mixed with normal work; mandatory numeric fields lead teams to "make up" numbers.

### 9.2. Inherent limitations of people and teams

Planning fallacy (Kahneman & Tversky), Hofstadter's law, *unknown unknowns* (especially with design and integration), immature designs, forgotten integration time, anchoring on the first value, knowledge concentrated in one person, unrecorded scope creep.

### 9.3. Proposed features

- Estimates as **ranges/probabilities** (optimistic–likely–pessimistic; Monte Carlo over historical throughput).
- A valid **"cannot estimate yet / exploring"** state.
- **Time-boxed spikes**, whose output is a decision or reduced risk, not code.
- A **risk register** linked to features (probability, impact, estimates affected, owner).
- **Early gates:** "design is stable enough" and "main integration verified" are conditions for detailed estimation.
- **Estimate history** and deviation to calibrate by team.
- **Re-forecasting each cycle**, showing the narrowing uncertainty (cone of uncertainty).
- Measure in order to improve the process, **not** to rank individuals.

---

## 10. Bottleneck detection and practice suggestions

Principle: **detect → explain with data → suggest a few related practices → the team decides → the app checks the results itself**.

### 10.1. Flow metrics

Cycle time, WIP, queue time per state, item aging, cumulative flow diagram (CFD), share/duration of blocked items (and recurring blockers).

### 10.2. Borrowed methods framework

| Method | Application |
|---|---|
| **Theory of Constraints** (5 steps: identify, exploit, subordinate, elevate, repeat) | Action framework for bottlenecks |
| **Kanban / Lean**, WIP limits, Little's law | Limits on work in progress |
| **Toyota Andon / Kaizen / 5 Whys** | Permission to "stop the line" and find root causes |
| **PDCA (Deming/Shewhart)** | Verification loop for suggestions |
| **OODA (Boyd)** | Dynamic decision-making, short loops |
| **Sun Tzu, Clausewitz** | Adapting to the team's "terrain"; accepting friction and fog |

### 10.3. Boundaries

Measure bottlenecks in the process, not in individuals; suggestions are proposals and the team may decline them with a reason; each suggestion has a review deadline; prefer simple, explainable rules before using AI.

---

## 11. AI agents automating work in the workflow

> Added from a side chat about the idea "an agent performs one node, team members control input/output". The other side chats (RBAC/ABAC, multi-source synchronization, software development models, MLflow, W&B...) **have not been read** and are not merged into this document.

### 11.1. Idea

A node in a workflow represents work, so the performer can be a **person, an automated tool, or an AI agent**. Agents are fast but can be confidently wrong, so the "team members control the agent's input and output" part is a **core feature**, not an add-on.

### 11.2. Four foundational principles

1. **The agent proposes, the gate decides.** Output is by default a *draft artifact*; only after passing the gate (automatic and/or human approval) does it become an official deliverable.
2. **An agent does not approve its own work** (permissions are separated, as for people).
3. **Each agent node has a team-member owner** who is responsible for the accepted output.
4. **Every run is traceable** like any other event.

### 11.3. Input control

| Mechanism | Description |
|---|---|
| **Input contract** | Declares what the agent receives: schema, artifact type, allowed sources |
| **Context package** | Shown to the team member before the run: what the agent will "see" |
| **Review and edit before running** | Remove items, add notes, mask sensitive data |
| **Data classification** | Public / internal / secret / unpublished; the agent receives only the allowed level |
| **Input snapshot** | Stores a hash and a copy of the input for reproduction and comparison |

**Security note:** content from external sources (PRs, comments, documents) is **untrusted data** and may contain malicious instructions (prompt injection). It must be treated as data to process, not as commands, and the agent's permissions must be limited so that malicious content cannot cause major harm.

### 11.4. Output control

- **Output contract:** required format (JSON schema, document template); wrong formats are rejected automatically.
- **Automatic checks first:** does it run, lint, schema, matches acceptance criteria, evaluation if available.
- **Human review (`needs_review`):** see the input/output diff; accept, edit and accept, or reject with feedback to re-run.
- **Record the human's edits:** valuable data for measuring agent quality and improving prompts.
- **Never write directly to core data:** the agent writes only to a draft area; "promote" is an action by a person or by the gate.

### 11.5. Autonomy level configured per node

| Level | Behavior | Suitable for |
|---|---|---|
| **0 – Suggest** | The agent only offers opinions; a person does the work | High-risk work, new trials |
| **1 – Draft + review** | The agent creates a draft; a person reviews each time | Default for most work |
| **2 – Self-run + automatic checks** | Passes automatic gates and moves on; a person reviews a summary | Repetitive work with clear tests (generating test cases, document formatting) |
| **3 – Autonomous with sampling** | A person only spot-checks randomly | Only once reliable long-term data exists |

The level should be **lowered automatically** when quality drops (for example, rejection rate rises), and raised only with evidence from history.

### 11.6. Operational guardrails

- **Tool allowlist:** for example, read a repo but not push; which APIs may be called; where it may write.
- **Budget limits:** tokens, cost, run time, number of retries; exceeding them stops the run and raises an alert.
- **Sandbox** for agents that run code.
- **Own identity:** the agent is an actor with its own account and permissions, not the permissions of the person who assigned the task.
- **Emergency stop button** (linked to Andon): a team member can stop a run.
- **Escalation:** when the agent is uncertain or checks fail repeatedly, the task goes back to a person with the reason.

### 11.7. Traceability and configuration

```
agent_run: id, node_id, token_id, agent_id, agent_version, model,
  prompt_version, input_snapshot_ref, output_artifact_ref,
  tool_calls (trace), cost, duration, status,
  reviewer_id, decision (accepted/edited/rejected), edit_diff_ref
```

```yaml
node: draft_acceptance_criteria
  kind: agent
  owner_role: BA
  agent: ba-assistant@v4
  input: [requirement_v3, glossary]
  output_schema: acceptance_criteria.v1
  autonomy: 1          # draft + review
  budget: { max_cost: 2.0, max_runs: 3 }
  gate: ba_review      # needs_review by the BA role
```

### 11.8. Examples by role

| Role | Agent does | Person does |
|---|---|---|
| **BA** | Drafts acceptance criteria from requirements | Reviews and edits |
| **QA** | Generates test cases from AC, proposes edge cases | Selects and adjusts |
| **Dev** | Opens a PR for a small bug fix | Reviewed by another developer + automatic checks |
| **Research** | Summarizes documents, runs analyses | Verifies cited sources before use |
| **AI/ML** | Proposes experiment configurations | Approves GPU cost before running |

### 11.9. Agent-specific risks

1. **Automation bias:** reviewers click "accept" out of habit. Show the parts the agent is unsure about, compare with sources, measure review time.
2. **Verification cost:** if review takes nearly as long as doing the work, the agent brings no benefit; measure acceptance rate and edit level per node.
3. **Unclear accountability:** define an owner and record who accepted.
4. **Data leakage** when context is sent to external models: policies based on data classification; option of self-hosted models.
5. **Lack of reproducibility:** models change over time; record model and prompt versions and treat the output as a snapshot.
6. **Using agent data to evaluate individuals:** avoid.

### 11.10. MVP recommendations and open questions

- The MVP only builds **levels 0 and 1**: input contract, context review/edit, output schema, human review gate, `agent_run` record. Higher levels open only when acceptance and edit-rate data supports it.
- **Open question:** is the agent **provided by the application itself** (integrating models and managing prompts), or an **external agent** that the customer connects through a standard interface (webhook, MCP)? The two options lead to very different security and accountability designs.

---

## 12. Consolidated feature list

Priority notation: **P0** = MVP, **P1** = phase 2, **P2** = later.

### A. Foundation

| # | Feature | Priority |
|---|---|---|
| A1 | Workspace, project, members, sign-in | P0 |
| A2 | Typed items, custom fields (JSONB with registered schema) | P0 |
| A3 | Typed links between items (including across projects) | P0 |
| A4 | Immutable event log, audit trail | P0 |
| A5 | Artifacts (commit, PR, build, file, document) | P0 |
| A6 | Multi-tenancy: `tenant_id` on every table + RLS; tenant routing layer | P0 |
| A7 | Silo DB / data regions for enterprises and universities | P2 |
| A8 | Comments, mentions, notifications | P0 |

### B. Work management

| # | Feature | Priority |
|---|---|---|
| B1 | Backlog, Kanban, boards, sprints | P0 |
| B2 | Timeline/Gantt, milestones, dependencies | P1 |
| B3 | Releases, changelog, versions | P1 |
| B4 | Templates per kind (software / research / ai) | P1 |
| B5 | Bugs: severity, lifecycle | P0 |

### C. Workflow and gates

| # | Feature | Priority |
|---|---|---|
| C1 | Workflow designer (nodes: source/input/process/switch/gate/output) | P1 |
| C2 | Gate with pass / fail / needs_review results | P1 |
| C3 | Gate rules as data, versioned | P1 |
| C4 | Checkpoints with snapshots, reproducibility | P1 |
| C5 | Limited loops, stale propagation when upstream changes | P1 |
| C6 | Separation of creator and approver | P1 |

### D. Data sources and integrations

| # | Feature | Priority |
|---|---|---|
| D1 | GitHub/GitLab connectors (webhooks + reconciliation polling) | P0 |
| D2 | Test result adapters (JUnit XML first) | P0 |
| D3 | Automatic commit/PR ↔ item linking (with manual linking) | P0 |
| D4 | Source snapshots with immutable identifiers | P0 |
| D5 | Connectors for CI, SonarQube, Figma, Confluence, Zotero, MLflow/W&B | P2 |

### E. Feature profile and traceability

| # | Feature | Priority |
|---|---|---|
| E1 | Feature profile gathering requirements/design/work/tests/defects/decisions | P0 |
| E2 | Timeline per feature | P0 |
| E3 | Change history with diffs for requirements/designs | P1 |
| E4 | Version tree (requirements, designs, PRs, test cases) | P1 |
| E5 | AC ↔ test ↔ result traceability matrix | P1 |
| E6 | Impact analysis when a requirement changes | P1 |
| E7 | Spiral / loops, cycle list with summaries | P2 |

### F. By role

| # | Feature | Priority |
|---|---|---|
| F1 | Many-to-many roles, permission matrix (role/permission) | P0 |
| F2 | Dedicated views: PO, PM, BA, QA, Dev | P1 |
| F3 | Artifact-level permissions | P1 |
| F4 | Test case repository, defect lifecycle, AC coverage | P1 |
| F5 | Release readiness report | P1 |
| F6 | Risk register, change requests, team capacity | P1 |

### G. Estimation and uncertainty

| # | Feature | Priority |
|---|---|---|
| G1 | Range/confidence estimates | P1 |
| G2 | "Cannot estimate yet" state, time-boxed spikes | P1 |
| G3 | Risk register linked to features | P1 |
| G4 | Estimate history and team calibration | P2 |
| G5 | Monte Carlo simulation, re-forecasting each cycle | P2 |

### H. Bottleneck detection and coaching

| # | Feature | Priority |
|---|---|---|
| H1 | Cycle time, WIP, queue time, aging, blocked rate | P1 |
| H2 | CFD | P1 |
| H3 | Bottleneck detection rules (conditions, thresholds, suggestions) | P1 |
| H4 | Practice suggestions with evidence, team accepts or declines | P2 |
| H5 | Self-evaluation of suggestion results after a deadline (PDCA) | P2 |
| H6 | Andon: permission to "stop the line" and attach alerts | P2 |

### I. Research and AI (modules per kind)

| # | Feature | Priority |
|---|---|---|
| I1 | Hypotheses, experiments, results (research kind) | P1 |
| I2 | Experiment run: parameters, metrics, dataset version, checkpoint (ai kind) | P1 |
| I3 | Run comparison, basic model registry | P2 |
| I4 | Record cancelled experiments with the reason as data | P1 |
| I5 | Trace claims in a manuscript ↔ original run/experiment | P2 |
| I6 | Links to Zotero, Overleaf, OSF | P2 |

### J. AI agents in the workflow

| # | Feature | Priority |
|---|---|---|
| J1 | `agent` node type with owner, input contract, output schema | P1 |
| J2 | Review and edit the context package before the agent runs | P1 |
| J3 | Output goes to a draft area, human review gate (see diff, edit, reject with feedback) | P1 |
| J4 | `agent_run` record (model/prompt version, input snapshot, trace, cost, decision) | P1 |
| J5 | Autonomy level 0–1 configured per node | P1 |
| J6 | Guardrails: tool allowlist, budget, agent's own identity, emergency stop, escalation | P1 |
| J7 | Data classification and policy for sending context to external models | P1 |
| J8 | Protection against prompt injection: treat external content as data, sandbox | P1 |
| J9 | Agent quality metrics: acceptance rate, edit level, review time; automatically lower autonomy | P2 |
| J10 | Autonomy levels 2–3 (self-run with automatic checks, sampling) | P2 |
| J11 | Integration of external agents through a standard interface (webhook, MCP) | P2 |

---

## 13. Assessment

### Strengths of the direction
- A **real** gap: current tools are either flexible without evidence, or have evidence without work management. No product yet connects hypothesis ↔ experiment ↔ manuscript ↔ task ↔ commit.
- The core model (item/link/artifact/event) is general enough for three project kinds without forcing a rigid process.
- Combining **gates + automatic evidence + human approval** can clearly differentiate the product from pure PM tools.
- The feature profile and traceability address the pain points that BA/QA/PO actually have, not only developers'.
- Making uncertainty visible runs against the direction of most current tools.

### Major risks
1. **Scope too broad:** building three kinds, a workflow engine, traceability, estimation and coaching at once will be very hard. Choose the narrowest slice to prove value.
2. **Gates prone to Goodhart:** passing the gate ≠ quality achieved; human approval is needed, and metrics must not be used to rank individuals.
3. **Data-entry burden:** if most events do not arrive automatically (Git, CI, tests), the product will repeat the "nobody records anything" failure of ELNs.
4. **Configuration complexity:** a workflow designer, multi-layer permissions and gate rules can become "a second Jira" without good defaults.
5. **Unvalidated assumptions:** pain point evidence comes mainly from vendor surveys; there is no direct data from research project managers or AI team leads.
6. **Competition and integration:** the "linking layer" approach on top of Git/CI/W&B/Zotero depends on connector quality and can be copied by large platforms.
7. **Team privacy:** person-level timelines, estimates and bottleneck detection easily become surveillance if not designed carefully.
8. **Agents increase the risk surface:** automation bias, prompt injection, data leakage to external models, non-reproducible output, verification cost equal to doing the work (details in section 11.9). MVP should stay narrowly at autonomy levels 0–1.

### Decisions leaning one way but not finalized
- Data structure: common columns + JSONB with schema (leaning toward the hybrid approach).
- Iteration = gate cycle or sprint (leaning toward gate).
- Pooled first, silo for enterprises.
- Practice suggestions are proposals, not rules.

### Decisions still open
1. First target group: software teams, university research labs, or AI teams?
2. Build from scratch or base on open source (Plane, OpenProject)?
3. Business model: commercial, internal, academic?
4. Target team size (individuals, 3–10 people, large organizations).
5. Platform: web, desktop, or mobile.
6. Agent provided by the application, or an external agent that the customer connects (webhook, MCP).

---

## 14. Suggested roadmap

| Stage | Content | Validation goal |
|---|---|---|
| **0. Discovery (4–6 weeks)** | Interview 8–10 people per group; short survey of 50–100 people | Confirm which pain points really hurt and who is willing to use the product |
| **1. Narrow MVP** | Core (A), GitHub + JUnit integration (D1–D4), feature profile + timeline (E1–E2), basic roles (F1), 1 kind | Teams see one place for one feature, with no manual entry |
| **2. Gates and traceability** | Workflow + gate (C), traceability matrix (E5), role-based views (F2), basic bottleneck detection (H1–H3), agents at levels 0–1 (J1–J8) | Gates usable for releases; BA/QA can work without seeing code |
| **3. Uncertainty and research/AI** | Range estimates, risks (G), research/ai modules (I), version tree | Estimates lose their "single number" and the product expands to other kinds |
| **4. Coaching and scale** | Practice suggestions, PDCA, silo DB, additional connectors | Adapted per team; ready for enterprise customers |

The MVP slice should sit at the **intersection of three things with automatically available data**: Git + test results + feature profile, for **one kind**; avoid building a full workflow designer from the start.

---

## 15. Next steps

1. Draft the **interview guide** for research PMs, AI team leads and the software team.
2. Finalize the **permission matrix as data** (`role`, `permission`, `role_permission`) and update the schema (`tenant_id` on every table, many-to-many roles).
3. Write a **complete one-sprint scenario** for a chatbot project (CHAT) with five roles, showing deliverables, evidence and gates.
4. Outline an **initial set of bottleneck detection rules** (conditions, thresholds, suggestions).
5. Outline a **standard schema** for GitHub snapshots and JUnit XML.
6. (Optional) Look up current pricing/features of the products and publishers' AI policies.
7. Finalize the agent model (provided by the application or external) and add `agent_run` and the `agent` node to the schema.
8. Read the remaining side chats (RBAC/ABAC, multi-source synchronization, software development model, MLflow, W&B, paper writing workflow) and merge them into the documents.

---

## Sources (from the search rounds)

- [Atlassian, State of Developer Experience 2025](https://www.atlassian.com/teams/software-development/state-of-developer-experience-2025)
- [JetBrains, Developer Experience & Productivity 2025](https://lp.jetbrains.com/devex-productivity-report-full-2025-dataviz/)
- [InfoWorld, The process is the problem for developers](https://www.infoworld.com/article/2212803/the-process-is-the-problem-for-developers.html)
- [DEV Community, Why do developers struggle with project management](https://dev.to/pratham_naik_project_manager/why-do-developers-struggle-with-project-management-and-how-to-fix-it-1bgp)
- [CLPMag, Survey: scientists frustrated with ELN systems](https://clpmag.com/lab-management/survey-scientists-frustrated-eln-systems/)
- [Liminal Bios, Why the lab notebook is failing scientists](https://liminalbios.substack.com/p/why-the-lab-notebook-is-failing-scientists)
- [Cureus, Tailoring project management resources for research teams](https://www.cureus.com/posters/3287-tailoring-project-management-resources-for-clinical-and-translational-research-teams-insights-from-user-experience-interviews)
- [University of Southampton, thesis on researchers' digital tools](https://eprints.soton.ac.uk/384916/1/Final_Thesis_Combined.pdf)
- [Comet, State of MLOps 2023](https://go.comet.ml/rs/912-JJP-445/images/Comet_State_of_MLOPs_Report-2023_ML_Practitioner_Survey.pdf)
- [Comet, press release 2022](https://www.comet.com/site/news-and-events/press-releases/2022/comet-releases-new-survey-highlighting-ais-latest-challenges-too-much-friction-too-little-ml/)
- [Chalmers, research on ML experiment management (2022)](https://www.cse.chalmers.se/~bergert/paper/2022-seip-mlexpmgmt.pdf)
