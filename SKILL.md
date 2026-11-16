---
name: northstar-ui
description: Design and build interfaces that are logical, calm, and polished across Web (HTML/CSS/JS), iOS (SwiftUI/UIKit), Android (Compose/XML), and desktop shells (IPA/APK/EXE). Use when creating or reviewing pages, organizing content and navigation, defining page regions and hierarchy, designing forms and states, implementing motion and micro-interactions (easing, duration, hold-to-confirm, like burst, slide-to-confirm, fly-to-cart, theme reveal, copy feedback, scroll and hover effects), or handling dark mode, accessibility, localization, and platform conventions. Core order: classify content, structure the page, establish hierarchy, then use motion to explain state change.
---

# Northstar UI: logic before pixels

> Ugly UI is rarely a color problem. It is unclassified content, unpartitioned pages, and missing hierarchy.
> Four moves, in order: **classify → structure → hierarchy → motion**. Good looks are a by-product of the third move; motion only explains state change.

**Read this first**: if you only have 10 minutes, read [`CHEATSHEET.md`](CHEATSHEET.md). For a new page, follow §0. Rubrics live in §1–§4. Pattern selection is §5. Run §6 before delivery.

**Works with any agent** that reads Markdown: Claude Code, Codex, Cursor, Gemini CLI, Windsurf, Aider, or a plain LLM. No runtime, no MCP server, no vendor lock-in.

---

## 0. Quick start

### 0.1 Change tiers (decide depth first)

| Change | Do this | Skip |
| --- | --- | --- |
| Copy / color / spacing / remove an element | Edit directly; inspect the change and its neighbors | §0–§2, DESIGN.md |
| New page | Content inventory + grouping (steps 1–2), then five zones | Fill only the 5 required DESIGN.md fields |
| New product / redesign / "make it beautiful" | All seven steps + DESIGN.md + slop audit | Nothing |

**"Never skip" applies only to new pages and redesigns.** Forcing a seven-step process on a one-line copy edit wastes time and invites mistakes on rules that do not apply.

### 0.2 Process (new page / redesign)

| Step | Action | Output | Failure sign |
| --- | --- | --- | --- |
| 0 | Fill the 5 required DESIGN.md fields + one-sentence north star | Direction, character, hero, do/don't | "Modern and clean" instead of a direction |
| 1 | List content and rough-group it | One line per item, labeled "what question it answers" | "We'll figure it out later" |
| 2 | Group with MECE | 3–7 groups, one job each | Two groups could merge, or one item fits two |
| 3 | Pick a page type and map five zones. **If none of the 10 patterns fit, go to §5 derivation** | What goes in nav / page header / toolbar / content / footer | Cannot say what the page header holds |
| 4 | Draw an ASCII wireframe | Boxes and order only | Opening the editor to write CSS |
| 5 | Set grid and three spacing tiers | 4–8 within group / 16–24 between groups / 32–48 between blocks | Everything has the same gap |
| 6 | Fill components + four type sizes | 28 / 20 / 16 / 13 | A fifth type size appears |
| 7 | Add four states + three screenshots + P0 | Default / empty / loading / error + 390 / 768 / 1440 | Only the default state |

**Gate: steps 1–2 must pass before step 3.** Four tests: every item has one home; every group has one job; peers do not overlap; 3–7 groups. Fail any, return to step 1. Do not hide a classification failure with color.

**Step 3 exit (one line):** if the product is not one of the 10 patterns, stop and go to §5—ask 2–3 shaping questions, then derive the structure; never force the nearest skeleton.

> Full walkthrough (dashboard from inventory to wireframe) in `topics/patterns.md` §12. Workflow rationale in `references/agent-ui-skills.md` §13.

### 0.3 Defaults (use these when unsure)

| Item | Default |
| --- | --- |
| Spacing | 4 base; 8 / 12 / 16 / 24 / 32 / 48 |
| Page padding | Mobile 16–20; tablet 24–32; desktop content ≤1280 |
| Type sizes | 28 / 20 / 16 / 13 (four only) |
| Line height | Chinese 1.7–1.8; Latin 1.45–1.6; headings 1.2–1.35 |
| Measure | Chinese 30–40 chars; Latin 45–75 chars; `65ch` |
| Radius | 8 / 12 / 16 / full; ≤3 per screen |
| Touch | iOS 44pt; Android 48dp; Web 44px |
| Motion | Press 80ms; micro 120–200ms; overlay 200–280ms; page 300–350ms; exit = enter ×0.7–0.8 |
| Easing | Enter ease-out; exit ease-in; movement ease-in-out; constant linear; bounce only for feedback |
| Accent area | ≤10%; semantic color ≤5% |
| Contrast | Body ≥4.5:1; large text/icons ≥3:1 |
| Elevation | ≤2 shadow levels per screen |
| Button height | 44–52 |
| Form columns | 1 on mobile; ≤2 on desktop, grouped by meaning |

### 0.4 Four lines and what they cure

- **Classification** cures "messy": peers do not overlap, like sits with like, unlike is separated (§1).
- **Structure** cures "drifting": five zones, each with one job; reading order is fixed (§2).
- **Hierarchy** cures "flat": four levers create priority; four type sizes per page (§3).
- **Motion** cures "abrupt": correct curves explain state change; only confirm, guide, or preserve continuity (§4).

The lines are serial. Bad classification makes structure wrong; bad structure makes polish irrelevant; before the first three, more motion is just decoration.

### 0.5 Direction is not an adjective

Formula: **Looks like `<a concrete world/object>`, because `<one executable decision>`.**

- Bad: "modern, clean, airy, tech-forward" — cannot decide whether a button is round.
- Good: "Like an engineering drawing: square corners, hairline rules, tabular numerals; only destructive actions carry color."

Difference test: place it next to two competitors. Can you name what is different in one glance? If not, choose again. Do not manufacture difference with decoration.

Default to **one direction** and state why. Explore 2–3 only when the user explicitly asks to compare directions or layouts. Borrowing a style, redesigning, and pixel-matching are three different jobs—confirm which one before you start.

---

## 1. Classification (decides "messy or not")

Four rules. Break any, return to step 1.

1. **MECE**: peers do not overlap; together they are complete. `Home / Dashboard / Data / Reports` contains overlaps → `Overview / Projects / Reports / Settings`.
2. **Same abstraction level**: one layer holds one level. "Sign out" is an action, not a settings category.
3. **3–7 per layer**: fewer than 3 needs no grouping; more than 7 needs another level, or low-frequency items (<20% of users) go under "More".
4. **Match the user's mental model**: `Orders / Order items / Refunds / Shipments` → `My orders / To receive / After-sales`.

Group with spacing: 4–8 within group, 16–24 between groups, 32–48 between blocks. Adjacent tiers must differ by at least one step; peers must be exactly equal; prefer spacing over dividers.

**Minimal settings-page sample**

Before: six bordered cards, twelve rows, "Sign out" third from the top, one radius and one shadow everywhere.

After:

```
Account
  Avatar        Lin            >
  Email         lin@ex.com     >
Notifications              (block gap 32)
  Push          [on]
  Weekly digest [off]
Danger                     (gap 48 from above)
  Sign out      (danger color)
```

13px group labels, 56px rows, no card borders, "Sign out" last and separated.

> More counterexamples, diagnostics, and the navigation decision tree: `topics/reference.md` §1 and `topics/patterns.md`.

## 2. Structure (decides "has a skeleton or not")

**Five zones, one job each:**

| Zone | Only job | Spec |
| --- | --- | --- |
| Navigation | Global destinations | Sidebar 240–280; top bar 56–64; mobile tabs 56–64 + safe area |
| Page header | Where am I, what is this, what next | 64–88 tall; title + one sentence + **one** primary action |
| Toolbar | What to do with this batch of content | 48–56; search, filter, sort, export |
| Content | Show or edit the subject | `max-width` 1280; body 65ch |
| Footer action bar | Finish or abandon this task | 56–72 + safe area |

- The page header answers "what is this"; the toolbar answers "what do I do with this batch"; global actions live in the top bar. Never merge the three.
- One filled primary button per screen; everything else is secondary, icon-only, or under `···`.
- Reading paths: list/detail/form = F; marketing/auth = Z; dashboard = F+Z.
- Keep the primary action above the third screen; on mobile, put it in the thumb zone; never put destructive actions bottom-right.

| Page type | Top to bottom |
| --- | --- |
| List | Page header → toolbar → list → pagination |
| Detail | Page header → 3–4 key facts → grouped details → danger zone |
| Form | Page header → grouped fields → sticky footer |
| Dashboard | Page header → toolbar → 3–4 KPIs → chart grid → detail table |
| Settings | Page header → 3–5 groups → danger zone (48 gap) |
| Auth | Brand → title + one sentence → 2–3 fields → one primary button |

The same page type must use the same order. A second visit should not require relearning.

## 3. Hierarchy (decides "beautiful or not")

Four levers, in priority order: **size → weight → space → color**. Shadow, gradient, and motion are levers five and six; if the first four fail, do not reach for them.

| Role | Size / weight | Used for |
| --- | --- | --- |
| Page title | 28 / 600 | One per page |
| Section title | 20 / 600 | Content groups |
| Body | 16 / 400 | Main text; card titles 16/600 |
| Auxiliary | 13 / 400 | Notes, timestamps, group labels |

- 60-30-10: neutral 60%, secondary 30%, accent 10%. Accent ≤10%, only for primary actions, selection, and key numbers.
- Three checks: squint (title/content/primary action still readable), grayscale (hierarchy survives without color), 3-second (a stranger can say what the page does and where to click).

> Slop audit and restraint: see "Slop audit" below.

## 4. Motion (decides "smooth or not")

One test: **remove the animation—does the user understand the state change less well?** Keep it only if yes.

| Motion | Curve | Duration |
| --- | --- | --- |
| Constant speed (progress, marquee, spin) | `linear` | by cycle |
| Exit (dismiss, collapse, close) | ease-in `.4,0,1,1` | enter ×0.7–0.8 |
| Enter (appear, expand, overlay) | ease-out `0,0,.2,1` | 180–260ms |
| In-page A→B (shared element, slider) | ease-in-out `.4,0,.2,1` | 220–320ms |
| Feedback spring (like, badge) | `.34,1.56,.64,1` | 300–450ms |

Three principles:

1. **Control duration**: UI motion ≤400ms; high-frequency actions take the low end; exits are shorter than enters.
2. **Use compositor-friendly properties**: only `transform` / `opacity`; progress via `scaleX`; layout changes via FLIP; DOM particles ≤80 (mobile ≤40); scroll via IntersectionObserver.
3. **Respect system settings**: static equivalent under `prefers-reduced-motion`; iOS `isReduceMotionEnabled`; Android switch instantly when animation scale is 0; haptics only for success/confirmation.

Six button recipes: hold-to-confirm, like burst, slide-to-confirm, fly-to-cart, theme circular reveal, copy feedback.
Sixteen UI effects: spotlight, magnetic button, hover lift, spring, shine sweep, scroll reveal, parallax, scroll narrative, count-up, text decode, typewriter, marquee, 3D tilt, stagger, edge sheen, easing comparison.

> All code, parameters, and SwiftUI/Compose equivalents: `topics/motion.md` (including §7 self-check).

---

## 4.5 Slop audit

**Statistical-default test**: swap the logo and product name. Does the page still look like "any AI product"? If yes, it is slop. The difference is not taste; it is whether someone made decisions.

Order matters: **Audit** (list each signal with location + severity) → **Direction** (character / visual world / 1–2 color moments / signature detail) → **Passes** (spacing → color → icons → copy → type → layout → details → imagery → motion). If the screen is already good, say so and suggest small refinements only.

Top 10 signals:

1. Purple-blue gradient + glow
2. Four equal KPI cards as "hierarchy"
3. Emoji as icons
4. One radius, one shadow, one spacing everywhere
5. Gray text on white
6. Blue "Delete account"
7. Glassmorphism / bento everywhere
8. Glowing orb / 3D blob as the hero visual
9. "Elevate / empower / all-in-one / reimagine"
10. Fake metrics / testimonials / countdowns

Fixes: one hero metric leads; use different shapes for different content; use real screenshots; danger color only for destructive actions; loading keeps full strength + spinner (never dim like disabled).

> Full 18-signal list and restraint rules: `topics/reference.md` §1.2.

---

## 5. Page pattern index

Ask first: what did the user come here to do (see / find / edit / compare / buy / sign in)? Then choose a pattern.

**There are only 10 skeletons here.** When the product is not one of them (downloaders, trading pipelines, IM workspaces, editor plugins, bookkeeping, inspections…):

1. **Do not force the nearest skeleton.**
2. Return to §0.2 step 1 and list the content inventory.
3. Ask 2–3 questions that decide the shape: Is the work parallel or serial? Should failures retry automatically? Is progress per task or per byte?
4. Apply the five zones and structure table in §2 to derive the new pattern.
5. Record the result in `DESIGN.md` (hero / primary action per screen).

One trigger question is worth more than 300 lines of presets.

| Pattern | Purpose | Core structure |
| --- | --- | --- |
| Admin dashboard | How are we doing | Sidebar + page header + KPIs + chart grid + detail |
| Data category | Find by dimension | Page header + toolbar + facets + grid |
| List + detail | Browse, then go deep | Left list 320–400 + right detail |
| Chat | Continuous conversation | Conversation list + message stream + fixed composer |
| Editor | Focus on one object for a long time | Top bar + toolbar + canvas + inspector |
| Settings | Scan and change configuration | Page header + 3–5 groups + danger zone |
| E-commerce listing | Compare and click | Filter bar + product grid + pagination |
| Auth | One job: sign in | Brand + 2–3 fields + primary button |
| Marketing | Persuade and drive one action | Hero + value props + proof + CTA |
| Result / empty | What happened, what next | Icon + title + explanation + primary action |

> Wireframes, dimensions, and mobile handling: `topics/patterns.md`. Shell differences for HTML/IPA/APK/EXE: `topics/delivery.md`.

---

## 6. P0 checklist (choose by change tier)

| Change | Required |
| --- | --- |
| Small edit (copy/color/spacing) | 9 (contrast), 14 (tokens) |
| New page | 3, 5, 6, 7, 11, 22 |
| New product / redesign | All 22 |

| # | Check |
| --- | --- |
| 1 | Every item has one home; every group has one job; peers do not overlap |
| 2 | Nav 3–5 (mobile) / 5–7 (desktop); in-page groups 3–7 |
| 3 | Page header: title + one sentence + one primary action |
| 4 | Toolbar exists or is explicitly unnecessary; jobs do not mix |
| 5 | Three spacing tiers 4–8 / 16–24 / 32–48, at least one step apart |
| 6 | ≤4 type sizes, from 28/20/16/13 |
| 7 | One filled primary button per screen |
| 8 | First-screen focal point is the most important information |
| 9 | Contrast from real hex: body ≥4.5, large text/icons ≥3 |
| 10 | Touch ≥44 (Web/iOS) / 48 (Android) |
| 11 | Four states, switching in the same container without layout jump |
| 12 | Persistent labels; placeholder is example only |
| 13 | Mobile reflows, not compresses; primary action in thumb zone |
| 14 | Dark mode uses semantic tokens; no hard-coded colors |
| 15 | Keyboard completes the primary flow; 200% text does not clip |
| 16 | Motion explains state; duration in range; exit shorter than enter |
| 17 | Only transform/opacity; no sustained Layout/Paint |
| 18 | Static equivalent under reduce-motion |
| 19 | Animation is interruptible and never blocks input |
| 20 | DESIGN.md matches the code |
| 21 | Slop audit Critical/High cleared |
| 22 | Real screenshots (390/768/1440) + hex contrast values |

**No evidence, no pass**: no screenshots, no computed values, no keyboard run = not accepted. If you cannot verify something, mark it as a **limitation**; do not disguise it as a pass.

Review report format, severity levels, and the evidence table: `topics/reference.md` §9.

---

## 7. Top 10 agent anti-patterns

1. Content in one flat column → group into 3–7 sections with labels
2. No page header/toolbar → apply the five zones in §2
3. Navigation is a feature list → merge, cut low-frequency items, use nouns
4. All type sizes and gaps look the same → four sizes + three spacing tiers
5. A divider under every row → spacing + group labels
6. Settings page as a stack of cards → 3–5 group list, 56px rows
7. Accent color everywhere → 60-30-10, ≤10%
8. Mobile is a compressed desktop → make 390 work first, reflow
9. Missing states → empty/loading/error in the same container
10. Every block fades + slides up, 500ms+ → explain state change only, ≤400ms

> Full 48-item list: `topics/reference.md` §1.

---

## 8. Topic index

| File | What it solves |
| --- | --- |
| [`CHEATSHEET.md`](CHEATSHEET.md) | One-page card: tiers, defaults, four curves, P0, token contrast, delivery |
| [`DESIGN.md`](DESIGN.md) | Design contract: facts, north star, hero, tokens, do/don't, quality gates |
| [`topics/motion.md`](topics/motion.md) | Motion tokens, 6 button interactions, 16 effects, reduced-motion, platform equivalents |
| [`topics/patterns.md`](topics/patterns.md) | 10 pattern wireframes, data-dense pages, full derivation example |
| [`topics/forms.md`](topics/forms.md) | Fields, labels, validation, special controls, auth |
| [`topics/states.md`](topics/states.md) | Seven states, empty/loading/error/offline, feedback choice, optimistic updates |
| [`topics/platforms.md`](topics/platforms.md) | iOS/Android/Web conventions, responsive, dark mode |
| [`topics/i18n.md`](topics/i18n.md) | Mixed CJK/Latin, punctuation, numbers/dates, RTL, font stacks |
| [`topics/delivery.md`](topics/delivery.md) | HTML / IPA / APK / EXE builds, signing, real-device acceptance |
| [`topics/recipes.md`](topics/recipes.md) | tokens.css, shell, buttons, dialog, toast, skeleton, list/settings/form pages |
| [`topics/reference.md`](topics/reference.md) | 48 anti-patterns, token contrast, type/color/icon/copy, accessibility, review format |
| [`references/agent-ui-skills.md`](references/agent-ui-skills.md) | Methodology comparison of 14 agent UI skills |

License: **Northstar UI License 1.0 (Attribution–NonCommercial)** — see [`LICENSE`](LICENSE). Third-party references and CC BY 4.0 attribution: [`NOTICE`](NOTICE).
