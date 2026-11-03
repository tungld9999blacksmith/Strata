# CLAUDE.md — Strata

Guidance for the AI assistant when working in this repo.

## Project

**Strata** is a workspace that connects work with evidence for software projects: each feature has one profile that gathers requirements, work items, commits/PRs, test results and decisions, updated from GitHub and CI. The MVP serves software projects only.

The repo currently contains documentation only (no application code yet).

## Canonical documents

| Content | File |
|---|---|
| Overview and diagrams | [README.md](README.md) |
| Product requirements (PRD) | [docs/product/PRD_Strata_MVP.md](docs/product/PRD_Strata_MVP.md) |
| Plan and phases | [docs/product/PLAN_Strata.md](docs/product/PLAN_Strata.md) |
| UI/UX principles, colors, fonts | [docs/design/Strata_UI_UX_Guidelines.md](docs/design/Strata_UI_UX_Guidelines.md) |
| System architecture | [docs/background/system-architecture.md](docs/background/system-architecture.md) |

When sources conflict, the priority order is: PRD, then the UI/UX guidelines, then the README.

## Working conventions

- **Document language:** English. Technical terms and codes (`KEY-n`, `snapshot`, `gate_run`) stay in English.
- **Interface language:** English by default, with a switch to Vietnamese. Labels in wireframes and mockups are written in English first.
- **Locked UI/UX decisions** (2026-11-03): open with the role-based Home page; mobile is read-only; keep the earth-tone palette and typography in the UI/UX document. See section 10 of that document.
- **Diagrams:** prefer SVG files in `assets/`, embedded with `![...](./assets/...)`. Do not add new Mermaid diagrams for mind maps or sitemaps.
- **Interface design:** before proposing colors, fonts, layouts or components, read [Strata_UI_UX_Guidelines.md](docs/design/Strata_UI_UX_Guidelines.md) and use the `strata-ui-design` skill.
- **Colors:** use only the tokens in table 2.3 of the UI/UX document. Do not add new colors without approval.
- **Metaphor:** geological layers for color; raw stone (source data) and crystal (normalized evidence) for symbols; gemstones are idea icons only (section 5.1 of the UI/UX document).
- **Contrast:** every text–background pair must meet WCAG AA before sign-off.
- **Privacy:** do not propose interfaces that rank or monitor individuals (README section 2.3.4, BR11; PRD section 7).
- **Source code is not stored** in Strata; BA/QA do not see diffs (README section 2.3.4, BR10; PRD section 7).

## Notes when editing files

- README.md uses CRLF line endings. When editing with scripts, keep CRLF.
- Do not commit or push unless asked.
- Do not delete or rename PRD codes (F1–F10, AC-Fx-xx, US, BR), because other documents reference them.
