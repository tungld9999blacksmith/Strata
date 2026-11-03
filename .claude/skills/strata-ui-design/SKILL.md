---
name: strata-ui-design
description: Design or review UI/UX for Strata (colors, fonts, layout, components, states, SVG diagrams) using the agreed earth-tone and neutral principles. Use when the user asks to design a screen, choose colors, fonts, components, wireframes, sitemaps, mind maps, or check contrast for Strata.
---

# Strata UI/UX design

Use this skill so that every Strata interface proposal stays consistent with the canonical documents.

## Canonical sources

Read these before answering:

1. [docs/design/Strata_UI_UX_Guidelines.md](../../../docs/design/Strata_UI_UX_Guidelines.md): colors, fonts, components, states, accessibility.
2. [docs/product/PRD_Strata_MVP.md](../../../docs/product/PRD_Strata_MVP.md): functional requirements and visibility by role.
3. [README.md](../../../README.md) section 4: sitemap, app shell, wireframes.

## Process

1. **Identify the screen and role.** Check against section 4.2 of the README (sidebar by role) and section 4 of the PRD (personas). State which role can see what.
2. **Pick colors from tokens.** Use only the tokens in section 2.3 of the UI/UX document. For each status, take the color from its geological layer (section 2.2) and the status chip (section 2.4).
3. **Check contrast.** Every text–background pair must reach 4.5:1 (normal text) or 3:1 (large text, icons, control borders). If a pair fails, switch to a variant in the table; do not create a new color.
4. **Check states.** Every status has a color, an icon and a text label. Loading, empty, error and no-permission states must all exist (section 6).
5. **Check typography.** EB Garamond headings from 20px up; IBM Plex Sans for content; IBM Plex Mono for codes and figures.
6. **Check layout.** 4px grid, no horizontal scrolling from 360px, one primary button per screen. On mobile, only reading and viewing features; hide editing actions.
7. **Check language.** Interface labels are in English (default); leave room for the Vietnamese version, which is about 20–30% longer.
8. **Check privacy.** No individual rankings; BA/QA do not see diffs or source code.
9. **Check symbols.** Per section 5.1: stone for raw data (commit, PR, CI run), crystal for normalized evidence (snapshot, test result, gate_run), gemstone only for ideas. Do not use these shapes as status icons, and do not use gemstones for scores or achievements.

## When creating SVG diagrams

- Save them in `assets/` and embed with `![...](./assets/...svg)`.
- Use the colors from section 2.5 of the canonical guidelines (diagram colors); do not pick colors at will.
- Keep text legible on both light and dark backgrounds when needed.
- Name files after their content, without spaces.

## How to present results

- State the screen, role and status under discussion.
- List the tokens used (name and hex) and the contrast ratios for each text–background pair.
- If something is uncertain or needs a reviewer, record it in section 10 (decisions to finalize) instead of deciding it yourself.

## Do not

- Do not add colors, fonts or font families beyond the canonical guidelines without approval.
- Do not use color as the only way to convey information.
- Do not propose individual rankings or personal monitoring.
- Do not commit or push unless the user asks.
