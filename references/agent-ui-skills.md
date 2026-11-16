# Agent UI Skills / Design-Intelligence Projects for Reference

> Note: all repositories below were verified to exist via the GitHub API (2026-11). This round is a **methodology comparison** only; no source code was copied. Each repository has a different license — check its `LICENSE` yourself before redistributing or using it commercially.
> This package itself uses MIT (see root `LICENSE`); third-party references and CC BY 4.0 attribution are described in `NOTICE`.
> What we absorb is the structure — "design judgment first, then generate code, then accept by evidence" — not any one repository's template.
> This round drew mainly on `heydamir/unslop`, `VoltAgent/awesome-design-md`, `bergside/awesome-design-skills`, `plugin87/ux-ui-agent-skills`, `dickwu/apple-design-skill`, and `oil-oil/oil-ui`; together they filled in "AI-slop audit, design contract, executable gates, review protocol, real-screen evidence."

## 1. anthropics/skills (Official Agent Skills)

- Repository: https://github.com/anthropics/skills
- Related skills: `skills/frontend-design`, `skills/canvas-design`, `skills/theme-factory`, `skills/web-artifacts-builder`.
- What it solves: breaks "frontend design" into skill instructions an agent can execute, stressing that the aesthetic direction and visual language come first, then the page; `canvas-design` focuses on composition, hierarchy, and visual review, `theme-factory` on theme tokens.
- What this project borrows:
  - A skill must be an **executable process + acceptance checklist**, not a mood board.
  - The visual layer fixes a few rules for "hierarchy/type/color/motion" before any component is written.
  - Before delivery, go through the checklist item by item instead of "it looks about right."
- What it doesn't copy: the official skills target general frontend generation; this project narrows the scope to the order "classification → structure → visual → motion," and adds three platforms (Web/iOS/Android) plus Chinese typography.

## 2. nextlevelbuilder/ui-ux-pro-max-skill

- Repository: https://github.com/nextlevelbuilder/ui-ux-pro-max-skill
- What it solves: gives an agent a "design intelligence" retrieval layer — matching palettes, fonts, layouts, and component suggestions by product type, industry, and style, so it doesn't start from guesswork every time.
- What this project borrows:
  - **Choose the style and design system before writing the page**; style isn't decoration, it must match the product type.
  - Replace long prompts with a "retrievable rule library"; this project compresses rules into tables and tokens an agent can look up directly.
  - Design review needs concrete values (font size, spacing, contrast), not adjectives.
- What it doesn't copy: it isn't tied to any style library; this project enforces hard constraints such as 60-30-10, four type sizes, and three spacing layers.

## 3. superdesigndev/superdesign

- Repository: https://github.com/superdesigndev/superdesign
- What it solves: an open-source AI product design agent supporting a workflow from requirements to interface options, component iteration, and visual review.
- What this project borrows:
  - **Design is an iteration loop**: generate → screenshot/compare → fix, not one-shot output.
  - Visual review must cover real states (empty/loading/error/mobile), not just the default state.
  - Motion is a design deliverable, not an "effect" bolted on after development.
- What it doesn't copy: it doesn't depend on its runtime or canvas; this project keeps only the "verifiable delivery checklist."

## 4. 21st-dev/magic-mcp

- Repository: https://github.com/21st-dev/magic-mcp
- What it solves: generates UI components from natural language in Cursor / Claude Code / Windsurf (MCP tools), wiring "component-level generation" into the agent workflow.
- What this project borrows:
  - Motion must land in the **component API** (states, events, accessible attributes), not just a block of CSS.
  - Components must compose: buttons/switches/dialogs share tokens and easing instead of each writing its own.
  - Generated code must run as-is, with a degradation path.
- What it doesn't copy: it isn't tied to its component marketplace; this project provides equivalent implementations in native HTML/CSS/JS and SwiftUI/Compose.

## 5. vercel-labs/agent-skills

- Repository: https://github.com/vercel-labs/agent-skills
- What it solves: Vercel's official Agent Skills collection, demonstrating how to package engineering practices (including frontend/deployment) into skills an agent can discover and execute.
- What this project borrows:
  - Skill directory structure: `SKILL.md` as the entry point + `topics/` loaded on demand, to control context length.
  - Trigger descriptions must state "when to use it," not just "what it is."
  - Each skill should be self-contained, verifiable, and have clear output.
- What it doesn't copy: it doesn't adopt its deployment/framework assumptions; this project stays framework-agnostic.

## 6. Also: ComposioHQ/awesome-claude-skills (Discovery Entry)

- Repository: https://github.com/ComposioHQ/awesome-claude-skills
- Purpose: an index aggregating Claude Skills of all kinds, useful for finding more "visual alignment/design review/component generation" skills.
- Usage advice: prefer repositories with a clear `SKILL.md`, a license, and example output; bookmarking without verification is a trap.

## 7. Also: obra/superpowers (Skill Engineering Reference)

- Repository: https://github.com/obra/superpowers
- Purpose: an agentic skills framework and development methodology; this project references its organization principle that "skills must be composable, testable, and handoff-ready," and takes no UI visual rules from it.

## 8. heydamir/unslop (AI-Slop Audit and Fixes)

- Repository: https://github.com/heydamir/unslop
- License: CC BY 4.0 (requires attribution and a link back to the repository).
- What it solves: first finds the AI slop in "vibecoded" interfaces — purple gradients, glows, emoji labels, shouting copy, uneven spacing — then sets a direction from the product and fixes it pass by pass, the way a designer redlines.
- What this project absorbs:
  - The three-stage **Audit → Direction → Passes**; the pass order is fixed: spacing → color → icons → copy → typography → layout → details → imagery → motion.
  - Direction must come from the product: personality, visual world, 1–2 color-carrying moments, one signature detail.
  - If the interface is already good, **say so and give only small refinements**; don't redo it to show effort.
  - An audit must point to **which file/section** has the problem, not vaguely say "it isn't premium enough."
- Maps to this skill: `SKILL.md` §3.5 / §3.6, `DESIGN.md` §9.

## 9. VoltAgent/awesome-design-md (the DESIGN.md Contract)

- Repository: https://github.com/VoltAgent/awesome-design-md
- What it solves: the `DESIGN.md` concept proposed by Google Stitch — a plain-text design system an agent reads to generate consistent UI; the repository collects design analyses of 84 real websites.
- What this project absorbs:
  - `AGENTS.md` governs "how to build," `DESIGN.md` governs "what it looks like"; keep them separate.
  - DESIGN.md must contain: brand/mission, type scale, palette, spacing, component families, accessibility, copy tone, Do/Don't, and measurable quality thresholds.
  - Use Markdown rather than a JSON schema; agents read it best and humans can maintain it.
- Maps to this skill: a new [`DESIGN.md`](../DESIGN.md) at the root, `SKILL.md` §0.4.

## 10. bergside/awesome-design-skills (Skill Package Structure)

- Repository: https://github.com/bergside/awesome-design-skills
- What it solves: 67 design skills in `SKILL.md + DESIGN.md` form, fetchable by provider; it separates "instructions for the agent" from "design notes for people."
- What this project absorbs:
  - `SKILL.md` holds executable rules, component families, and quality thresholds; `DESIGN.md` holds direction, rationale, and maintenance notes.
  - A skill should be fetchable, previewable, and persistable, with a stable structure independent of any CLI.
  - Quality thresholds are written as checkable items, not "please ensure high quality."
- Maps to this skill: the directory structure and §8 index; the "quality thresholds" in `DESIGN.md`.

## 11. plugin87/ux-ui-agent-skills (52 Executable Gates)

- Repository: https://github.com/plugin87/ux-ui-agent-skills
- What it solves: instead of only design doctrine, it uses 52 gates to check what the agent actually built: contrast in a real browser, axe roles/landmarks, keyboard, target size, reduced motion, 280px overflow, token use by intent, and screen economy.
- What this project absorbs (written into `SKILL.md` §6.1):
  - **Gates over claims**: "should be fine" is not acceptable; demand screenshots and computed values.
  - Contrast is computed **per state** (default/hover/disabled/loading), not just for body text.
  - No horizontal overflow at 280px; treat touch size and the WCAG 2.2 target size separately.
  - Destructive actions must wear the danger color, regardless of theme; loading must not borrow disabled's faded look.
  - "Screen economy": one hero metric leads, three blocks use different shapes, don't build three identical cards.
  - No hard-coded values; use tokens by intent.
- Maps to this skill: §6.1 evidence table, §3.5 slop table, P0 items 17/20/22.

## 12. dickwu/apple-design-skill (Review Protocol)

- Repository: https://github.com/dickwu/apple-design-skill
- What it solves: cross-platform UI/UX review based on Apple HIG, auditing through five lenses and outputting a review report in a fixed format.
- What this project absorbs (written into `SKILL.md` §6.1):
  - Set the context before reviewing: platform/framework, users, artifact type, design thesis, user goals.
  - Five lenses in order: accessibility → platform conventions → hierarchy/layout → copy → craft.
  - Fixed report structure: Summary / Critical / Improvements / Craft notes / What works; each issue carries What / Why / Fix plus severity.
  - **"A limitation is not a finding"**: if a screenshot can't show contrast, mark it as a limitation; don't invent a conclusion.
  - When there is no Critical/High, write a short note; don't manufacture problems.
- Maps to this skill: the review output template and severity definitions in §6.1.

## 13. oil-oil/oil-ui (Design Workflow)

- Repository: https://github.com/oil-oil/oil-ui
- What it solves: an interface design workflow for AI coding agents: product facts → protagonist/action → grayscale structure → implementation → real-screen review → verified delivery; recommend only one direction by default.
- What this project absorbs:
  - **Single direction by default**; explore multiple directions only when the user explicitly asks for a comparison, so "three options" can't stall the decision.
  - Get the grayscale wireframe right first, then add color/type/key visuals; if the structure doesn't work, go back — don't mask it with visuals.
  - One protagonist and one primary action per screen; merge or demote actions of equal weight.
  - **No visual acceptance without seeing the real screen**; judge motion by frame sequences or actual interaction.
  - Never fake business success, customer quotes, or product metrics; put sample data in the delivery notes, not in the interface.
- Maps to this skill: §0.4 / §0.5, §2.3, §6.1.

## 14. uxuiprinciples/agent-skills (Review Role Split)

- Repository: https://github.com/uxuiprinciples/agent-skills
- What it solves: five `SKILL.md` files take on interface evaluation, UX audit, flow inspection, and vibe-coding advice, so review isn't limited to one perspective.
- What this project absorbs:
  - Review can split into four independent lenses — visual / interaction / flow / copy — so one lens isn't forced to cover everything.
  - Each lens answers only its own questions; merge them into one report at the end.
  - Small changes don't need the full review; pick lenses by risk.
- Maps to this skill: the evidence table and review format in §6.1; for major redesigns, run all four lenses.

## Comparison Conclusion: Gaps This Skill Deliberately Fills

| Common Agent UI skill | Common gap | How this project handles it |
| --- | --- | --- |
| Style/palette retrieval | Gives style only, no page structure | §1–2 start with MECE classification and five regions |
| Component generation | Components look fine, but states and accessibility are missing | Four states/seven states + 22 P0 items |
| Visual review | Only looks at the default state in a screenshot | 390/768/1440 + empty/loading/error + real hex contrast |
| Motion | Only "fade/slide" or a pile of parameters | Four motions mapped to four curves + duration tokens + 6 button recipes + 16 effects |
| Platform adaptation | Web only | iOS/Android/Web conventions and equivalent APIs |
| System settings | Ignores reduce motion | One of three principles, with CSS/JS/native detection |
| **Design contract** | Rules scattered in prompts, lost across sessions | New `DESIGN.md`: 10 sections, fillable, verifiable |
| **AI slop** | Can generate "pretty," but reads as AI at a glance | §3.5 eighteen slop signals + Audit/Direction/Passes |
| **Acceptance** | Verbally "done" | §6.1 evidence table: screenshots, computed values, keyboard, 280px, tokens |
| **Review** | Only "something feels off" | Fixed report format + What/Why/Fix + four severity levels |
| **Direction** | Three options at once stall the decision | Single direction by default; explore only when a comparison is requested |

## Three Rules for Citing These Repositories

1. **Borrow methods, not assets**: don't lift palettes, illustrations, icons, or brand material directly; if reusing code snippets, check the license and attribution requirements first.
2. **Verify before citing**: repository paths, skill directories, and licenses change; open them to confirm before writing them into a deliverable.
3. **Don't mistake "style" for "structure"**: no visual reference can rescue a classification error; the order is always classification → structure → visual → motion.
