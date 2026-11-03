# Strata — UI/UX Design Principles

> This document is the **canonical source** for Strata's colors, typography, components and interactions. If it conflicts with section 4 of the [README](../../README.md), this document wins and the README must be updated accordingly.
> Status: **v0.2, finalized on 2026-11-03** (decisions UX-Q1 to UX-Q5 in section 10). Color values have been checked for WCAG contrast.

---

## 1. Core UX principles

| # | Principle | How it is expressed |
|---|---|---|
| U1 | **Visible evidence** | Every "verified" status comes with a text label and a link to the snapshot or source. |
| U2 | **Features are central** | From an item, PR or test there is always a path back to the Feature profile. |
| U3 | **Simple by default** | Fixed workflow, little configuration. New users can start within 30 minutes. |
| U4 | **Measure the process, not people** | No individual leaderboards. Metrics are shown per team, feature or action. |
| U5 | **Explainable** | The gate shows each condition, the measured value, the threshold and the reason for pass/fail/needs_review. |
| U6 | **Hide code by default for BA/QA** | Show only metadata, title, status and GitHub link. No diffs. |
| U7 | **Start by role** | After sign-in, users land on the role-based Home page (P02) with the "needs action now" items for their role. |
| U8 | **One unified metaphor** | The geological layers provide the **colors** (section 2.2); raw stone → crystal provides the **shapes and data symbols**; gemstones are only **idea icons** (section 5.1). |

---

## 2. Colors

### 2.1. Color principles

1. **Neutrals cover 80–90% of the area.** Backgrounds, cards, borders and text use warm-toned neutrals. Accent colors are used only for primary actions and statuses.
2. **Normal text reaches a contrast of ≥ 4.5:1.** Large text (≥ 24px, or ≥ 18.7px bold), icons and control borders reach ≥ 3:1. Verify with a tool, not by eye.
3. **Text on colored backgrounds is chosen by threshold.** White text on sufficiently dark fills; black `#0B0B0B` text on light fills (for example ochre).
4. **One color, one meaning.** Status colors are never used decoratively. The same color must not change meaning between screens.
5. **Never rely on color alone.** Every status has a text label or an icon. Red–green is not the only way to distinguish anything (about 8% of men have red–green color blindness).
6. **Every token has a light and a dark value.** Dark mode is not a simple inversion. Accent colors are slightly lighter, and every text–background pair is re-checked.
7. **No colors outside the token table.** If a new color is needed, propose it in section 2.4 first, then use it.

### 2.2. Meaning of colors by geological layer

Each color is tied to a **layer** of the system. Principle: the deeper the layer, the more stable it is; the more it surfaces, the more it is changing.

| Layer (source color) | Meaning in Strata | Used for | Display example |
|---|---|---|---|
| **Bedrock** (charcoal stone) | Immutable evidence, cannot be edited | Snapshots, events, gate_run, logs | "Immutable" label with a lock icon |
| **Sand** (warm cream) | Document background, reading content | Description cards, quote blocks, decisions | Background of a "Decision" block |
| **Umber** (brown soil) | Work in progress | Items, board, `in_progress` | Item card in progress |
| **Terracotta** (fired clay) | Gates and primary actions | Primary buttons, gates, approvals, CTAs | "Request completion" button |
| **Ochre** (weathered soil) | Waiting or outdated | `needs_review`, `stale`, waiting for QA | "Needs review" chip |
| **Mineral green** | Verified, passed | `pass`, verified AC | "Verified" chip |
| **Rock red** | Errors, blocked | `fail`, failing tests, rule violations | "Failed" chip |
| **Plum** (rock violet) | Decisions and traceability | Decision comments, AC–test links | "Decision" marker |

### 2.3. Token table

Hex values are the values finalized for v0.1. The ratio column is the contrast against the background of the same mode.

**Neutrals (backgrounds, borders, text)**

| Token | Light | Dark | Used for |
|---|---|---|---|
| `bg` | `#F9F9F7` | `#0B0B0B` | Page background |
| `surface` | `#FFFFFF` | `#1A1A19` | Cards, panels |
| `surface-raised` | `#FCFCFB` | `#20201F` | Popovers, drawers |
| `surface-sunken` | `#F3F3F0` | `#151515` | Nested areas, striped tables |
| `border` | `#D2D1C7` | `#383835` | Regular borders |
| `border-strong` | `#A5A49A` | `#5F5E5A` | Control borders, input fields |
| `text` | `#0B0B0B` (18.7:1) | `#F0EFEC` (17.1:1) | Primary text |
| `text-2` | `#52514E` (7.5:1) | `#A5A49A` (7.0:1 on `surface`) | Secondary text |
| `text-3` | `#6F6D67` (5.2:1 on `surface`) | `#8A8880` (4.9:1 on `surface`) | Muted text, labels, metadata |

**Layer colors (fills and text)**

| Token | Light fill | Light text (on `bg`) | Dark fill | Dark text (on `bg`) | Notes |
|---|---|---|---|---|---|
| `bedrock` | `#3E444B` | — | `#B4B3A8` | — | White text on the light fill: 9.8:1 |
| `sand` | `#F5F1E8` | — | `#2C2C2A` | — | Background only, not for text |
| `umber` | `#7A4E2D` | — | `#C08A5B` | `#C08A5B` (6.6:1) | White text on the light fill: 7.1:1 |
| `terracotta` | `#A3482A` | `#A3482A` (5.7:1) | `#D97757` | `#D97757` (6.3:1) | White text on the light fill: 6.0:1; black text on the dark fill: 6.3:1 |
| `ochre` | `#F0AC54` | `#8F5B12` (5.4:1) | `#F0AC54` | `#F0AC54` (10.1:1) | On light fills use `#0B0B0B` text (10.1:1) |
| `mineral` | `#3F7A67` | `#3F7A67` (4.8:1) | `#8FBFAE` | `#8FBFAE` (9.6:1) | White text on the light fill: 5.0:1 |
| `fail` | `#B23A2E` | `#B23A2E` (5.6:1) | `#E07A6E` | `#E07A6E` (6.7:1) | White text on the light fill: 5.9:1 |
| `plum` | `#6B66A8` | — | `#A7A2D6` | `#A7A2D6` (8.2:1) | White text on the light fill: 5.1:1 |

**Focus ring:** light `#7A4E2D` (6.7:1 on `bg`), dark `#C08A5B` (6.6:1 on `bg`). A 2px ring, offset 2px from the control.

> **Note on slide colors:** `#C6613F` (the clay color in the slides) is not used as text on light backgrounds because it only reaches 3.8:1. Use `#A3482A` for text and for fills with white text.

### 2.4. Status chips

Every status has **a color + an icon + a text label**. No component is dropped.

| Status | Layer | Icon | Label | Light fill / text | Dark fill / text |
|---|---|---|---|---|---|
| Verified (`pass`) | mineral | ✓ | Verified | `#3F7A67` / white | `#8FBFAE` / `#0B0B0B` |
| Needs review (`needs_review`) | ochre | ◐ | Needs review | `#F0AC54` / `#0B0B0B` | `#F0AC54` / `#0B0B0B` |
| Outdated, needs confirmation (`stale`) | ochre | ⟳ | Needs confirmation | `#F0AC54` / `#0B0B0B` | `#F0AC54` / `#0B0B0B` |
| Failed (`fail`) | fail | ✕ | Failed | `#B23A2E` / white | `#E07A6E` / `#0B0B0B` |
| Uncovered | bedrock, light | ○ | Uncovered | border `border-strong`, text `text-2` | border `border-strong`, text `text-2` |
| Immutable | bedrock | lock | Immutable | `#3E444B` / white | `#B4B3A8` / `#0B0B0B` |
| Decision | plum | flag ⚑ | Decision | `#6B66A8` / white | `#A7A2D6` / `#0B0B0B` |

Status icons do not use stones, crystals or gems (those are reserved for object types and ideas, see section 5.1). No emoji in the interface; use vector icons.

### 2.5. Colors in diagrams (SVG)

Diagrams in `assets/` (mind maps, sitemap) use the same token table. Each group takes the color of the closest-meaning layer, so the colors in diagrams do not contradict the colors in the interface.

| Group in the diagram | Token | Hex | Reason |
|---|---|---|---|
| General system, Foundation, Administration | `text-2` (neutral) | `#52514E` | Infrastructure, carries no status |
| Evidence | `bedrock` | `#3E444B` | Immutable evidence |
| Work | `umber` | `#7A4E2D` | Work in progress |
| Features | `terracotta` | `#A3482A` | Product core, primary actions |
| Quality | `mineral` | `#3F7A67` | Verification; its meaning matches the "verified" status, so it is allowed |
| Integrations | `plum` | `#6B66A8` | Tracing evidence sources |

Rules:

- Group titles: white text on a solid fill. Every fill in the table reaches ≥ 5:1 with white text.
- Child boxes: background is the group color at 6–12% strength on `surface`, border is the group color, text is `text` (`#0B0B0B`).
- Connector lines: `border-strong` (`#A5A49A`) for trees; the group color for mind map branches.
- Diagram titles use EB Garamond (fallback Noto Serif, Times New Roman); other text uses IBM Plex Sans. Do not use Georgia as a fallback because it renders Vietnamese diacritics incorrectly.
- Do not use `fail` or `ochre` for groups in diagrams, because these two colors only carry status meaning.
- The diagram background is `surface` (`#FFFFFF`).

---

## 3. Typography

### 3.1. Font families

| Role | Font | Reason | Weight |
|---|---|---|---|
| Headings | **EB Garamond** | Academic feel, fits the imagery of soil layers and archives. Already used in the slides. | 500–600 |
| Interface and content | **IBM Plex Sans** | Supports Vietnamese, many weights, readable at small sizes. | 400, 500, 600 |
| Code and figures | **IBM Plex Mono** | Fixed width for `KEY-12`, SHA, run ids and test numbers. | 400 |

Do not add a fourth font family.

### 3.2. Type scale (desktop first)

| Token | Font | Size / line height | Weight | Used for |
|---|---|---|---|---|
| `display` | EB Garamond | 32 / 1.2 | 600 | Page titles (H1) |
| `h2` | EB Garamond | 24 / 1.25 | 600 | Content blocks (H2) |
| `h3` | EB Garamond | 20 / 1.3 | 500 | Cards, drawers (H3) |
| `body` | IBM Plex Sans | 16 / 1.5 | 400 | Body text, descriptions, AC |
| `ui` | IBM Plex Sans | 14 / 1.4 | 400–500 | Labels, tables, menus, forms |
| `caption` | IBM Plex Sans | 12 / 1.4 | 500 | Captions, metadata |
| `mono` | IBM Plex Mono | 14 / 1.4 | 400 | Item codes, SHA, test numbers |

### 3.3. Rules

- The smallest text is **12px**. Main content on mobile stays at **16px**.
- EB Garamond is used only from **20px** upward. It is not used for labels, buttons or tables.
- Numbers in tables and cards use `font-variant-numeric: tabular-nums` so that columns line up.
- Maximum line length is about **70 characters** for descriptions and acceptance criteria.
- Load Google Fonts with the correct Vietnamese subset and only the weights in use.

---

## 4. Layout, spacing and shape

| Property | Value |
|---|---|
| Base grid | 4px. Spacing uses multiples of 4 (4, 8, 12, 16, 24, 32, 48). |
| Radius | Controls 6px; cards 8px; chips 999px (pill shape). |
| Shadows | Minimal. Cards use borders instead of shadows; drawers and popovers have a light shadow. |
| Width | Desktop first. No horizontal scrolling at 360px and above. |
| Mobile margins | 16px on both sides. |
| Mobile | **Read-only viewing.** View the home page, feature profiles, timelines, test results and gate status. No drag-and-drop, no item edits, no gate approval, no rule edits on mobile; these buttons are hidden with the note "Open on desktop to act". |
| Sidebar | Based on role (see README section 4.2). Items the user has no permission for are not shown. |

---

## 5. Key components

| Component | Rule |
|---|---|
| **Primary button** | `terracotta` fill, white text (light mode) or `#0B0B0B` (dark mode). Each screen has only one primary button. |
| **Secondary button** | `border-strong` border, `surface` background, `text` text. |
| **Danger button** | `fail` fill; always has a confirmation step. |
| **Status chip** | According to section 2.4. Never color only. |
| **Item card** | `KEY-n` code (mono), title, assignee, PR/CI chips, severity if it is a bug. |
| **Feature profile** | Tabs with deep links. Fixed tab order: Summary, AC, Work, PR/CI, Tests, Decisions, Timeline. |
| **Gate** | Expandable panel: list of conditions, measured value, threshold, result of each condition. |
| **Evidence** | Every test figure or "verified" AC is a link to the snapshot. |
| **Drawer** | Item details without leaving the list. `Esc` closes it. |
| **Undo toast** | Drag-and-drop and status changes can be undone within 5 seconds. |

### 5.1. Symbol language: stone, crystal, gemstone

Alongside the geological layers (used for color), Strata uses three mineral concepts for **shapes and symbols**. They tell the same story as the product's data flow: raw data from GitHub/CI (**stone**) is normalized into structured evidence (**crystal**); the **gemstone** represents only ideas.

```text
Raw stone (source data) ──normalize──▶ Crystal (structured evidence) ──gate──▶ decision
Gemstone = idea (stands apart, not part of the data flow)
```

| Concept | Shape | Meaning | Used for | Not used for |
|---|---|---|---|---|
| **Stone** | Irregular polygon, slightly rounded corners (2px), asymmetric | Raw data, arrived from a source, not yet processed | Icons for artifacts: commit, PR, CI run, original JUnit file; "unlinked" artifacts; empty-state illustrations | Status, gate results |
| **Crystal** | Hexagon or symmetric polyhedron, sharp edges, with a cross-section | Normalized evidence, structured, reproducible | Icons for snapshots, test results, AC matrix, gate_run; illustrations once evidence exists | Pass/fail status (use the chips in section 2.4) |
| **Gemstone** | Cut gem (table facets and bevels) | **Idea icon only** | Idea notes, proposals for new features, tips and onboarding hints | Status, scores, rewards, rankings, priorities |

Rules:

1. **Symbols indicate the object type, not the status.** Status is always a chip (color + status icon + label) according to section 2.4.
2. **Gemstones carry no value.** Do not use gemstones for achievements, badges or individual "points" (per U4: do not measure people).
3. **One color.** Icons use `currentColor`, matching the surrounding text color. Only large illustrations (empty states, onboarding) are filled with layer tokens; do not create new colors for stones or crystals.
4. **Icon grid:** 16, 20 and 24px; stroke 1.5px; stones have 2px rounded corners, crystals and gemstones have sharp corners so they are distinguished by shape rather than color.
5. **Keep it minimal.** Each screen has no more than one mineral illustration; icons in tables and lists stay simple.
6. **Accessibility.** Meaningful icons carry a text label or `aria-label`; decorative icons use `aria-hidden="true"`.
7. **Icon source:** use one line-icon set for regular icons (for example Lucide, which has `gem` and `hexagon`); draw the stone and crystal icons by hand following rule 4, so the stroke weight matches.

---

## 6. Interface states

| State | Appearance | Action |
|---|---|---|
| Loading | Skeleton matching the layout | None needed |
| Empty | Short explanation and an action button | For example: "Connect GitHub" |
| Error | Message with an error code, without technical details | Retry; contact Admin if it is a permission issue |
| No permission | 403 page stating the required role | Request permission from Admin |
| Degraded integration | `degraded` banner | Check health; wait for polling to compensate |
| Outdated evidence | `stale` chip | QA confirms or updates |

---

## 7. Accessibility

- Every text–background pair meets the contrast requirements in section 2.1.
- Every action can be done with the keyboard. Tab order follows reading order.
- The focus ring is always visible (section 2.3).
- Minimum tap target 24×24px; 40px is preferred for primary buttons.
- Respect `prefers-reduced-motion`: turn off decorative motion.
- Language: **English by default**, with a switch to Vietnamese (see PRD section 8). The language choice is saved per user.
- No information is conveyed by color, sound or position alone.

---

## 8. Content and tone

- Interface labels are written in English first; the Vietnamese version uses the same string keys. Labels are short, and buttons use verbs (for example "Create item" / "Tạo item", "Review gate" / "Duyệt gate").
- Layouts leave room for the Vietnamese version, which is usually about 20–30% longer than English.
- Terms follow the PRD: feature, item, snapshot, gate, decision, AC.
- Do not use words that judge people ("poor", "slow"). Describe the process instead ("waiting for review").
- Every error message states the next action to take.

---

## 9. Checklist before design approval

- [ ] Uses the exact tokens in section 2.3; no colors outside the table.
- [ ] Every text–background pair has been checked for contrast.
- [ ] Every status has a color, an icon and a text label.
- [ ] Stones, crystals and gemstones are used as defined in section 5.1; gemstones are used only for ideas.
- [ ] Only one primary button per screen.
- [ ] Typography follows section 3; no fourth font family.
- [ ] The screen works from 360px and text is not clipped.
- [ ] Loading, empty, error and no-permission states exist.
- [ ] BA/QA cannot see diffs or source code.
- [ ] No individual rankings or personal metrics.
- [ ] Both light and dark versions exist.

---

## 10. Decisions finalized

| ID | Question | Decision (2026-11-03) | Applies to |
|---|---|---|---|
| UX-Q1 | Default opening screen | Role-based Home page | Section 1 (U7) |
| UX-Q2 | Default language | English, with a switch to Vietnamese | Sections 7, 8 |
| UX-Q3 | Keep or redo the visual brand | Keep the earth-tone palette and typography in this document | Sections 2, 3 |
| UX-Q4 | Mobile support level | Read-only viewing; actions on desktop | Section 4 |
| UX-Q5 | Move the SVGs in `assets/` to the new tokens | Yes, after the token table is finalized | Section 2.5; applied to the 3 SVGs in `assets/` |

---

## Related documents

- [README](../../README.md) section 4 (initial UI/UX principles, sitemap, key screens, current UI prototype)
- [Strata_UI_Prototype.md](Strata_UI_Prototype.md) (screens exported from the Figma file, with notes)
- [PRD](../product/PRD_Strata_MVP.md) section 4 (personas), section 6 (feature requirements)
- [CLAUDE.md](../../CLAUDE.md) (instructions for the AI assistant in the repo)
- Skill: `.claude/skills/strata-ui-design/SKILL.md`
