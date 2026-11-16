# Values and Anti-Patterns Quick Reference

> This topic covers: the concrete values to check when writing or reviewing code, plus 40 general anti-patterns.
> Classification, structure, and hierarchy principles live in the main file; full motion recipes in `motion.md`; the 18 AI-slop signals in `SKILL.md` §3.5; only "fix-it-like-this" details belong here.

## 1. 40 General Anti-Patterns

| # | Anti-pattern | Fix |
| --- | --- | --- |
| 1 | placeholder as label | Persistent label; placeholder for examples only |
| 2 | Key information in small light-gray text | Raise contrast/type size, or demote to secondary |
| 3 | Two solid primary buttons on one screen | Keep one primary only |
| 4 | Long paragraphs centered | Left-align + 65ch |
| 5 | One color per heading level | Layer with type size/weight |
| 6 | Everything in a card | Use lists/sections for continuous content |
| 7 | Icon alone conveying vague meaning | Pair with text or tooltip |
| 8 | Touch target < 44 | Enlarge hit area |
| 9 | Submit disabled by default with no explanation | Allow click and show field errors |
| 10 | Error on every keystroke | Validate on blur or submit |
| 11 | Full-screen spinner for everything | Skeleton/partial loading |
| 12 | Overlay inside overlay | Merge the flow or use a page |
| 13 | Red for ordinary emphasis | Red only for danger/error |
| 14 | Autoplay carousel with no pause | Static by default, manual switching |
| 15 | Bottom nav + hamburger together | Pick one navigation model |
| 16 | Fixed height for variable text | min-height + auto fit |
| 17 | No empty/error/loading states | Design seven states |
| 18 | Toast for critical errors | In-place error / banner |
| 19 | Custom scroll hijacking | Use native scrolling |
| 20 | Pure black and pure white in dark mode | Near-black + off-white |
| 21 | Shadows/gradients everywhere | Pick one elevation strategy |
| 22 | Generic stock photos | Product screenshots/custom illustration/no image |
| 23 | Skeleton shape mismatches content | Build skeletons from the real layout |
| 24 | Confirm dialog with only OK/Cancel | Verb + object + quantity |
| 25 | Long sentences in all caps | Normal sentence case |
| 26 | Autoplaying sound/video | Muted by default/manual |
| 27 | Infinite scroll with no end | Paginate or state "you're at the end" |
| 28 | Horizontal scrolling on mobile | Vertical list/cards |
| 29 | Blocking paste/autofill | Allow it, sanitize if needed |
| 30 | Removing focus outline | Custom visible focus ring |
| 31 | Legal text tiny and gray | At least 12–13, expandable |
| 32 | Multiple words for one action | Build a glossary |
| 33 | Loading causes layout shift | Reserve dimensions |
| 34 | Submit without loading | loading + double-submit guard |
| 35 | Status conveyed by color alone | Add icon/text/shape |
| 36 | Nested scroll regions | Avoid inner scrolling |
| 37 | Fixed bottom bar covering content | Reserve padding |
| 38 | Excessive animation | Animate only to explain/confirm/guide |
| 39 | Fake urgency/fake stock | Real information |
| 40 | Form fields without labels | Persistent labels |

## 1.1 Additional Motion Anti-Patterns

| # | Anti-pattern | Fix |
| --- | --- | --- |
| 41 | fade + slide-up on every block | Only on first entry/level change |
| 42 | Animations 500ms+, exit slower than enter | UI animation ≤ 400ms; exit ×0.7 |
| 43 | bounce on layout/progress | Spring only for likes, badges, drag snap-back |
| 44 | Ignoring reduce motion | Media query + JS check + static equivalent |
| 45 | Animating `width`/`height`/`top`/`box-shadow` | Use `transform`/`opacity`/`clip-path`; FLIP |
| 46 | One `ease` for every element | Ease-out for enter, ease-in for exit, ease-in-out for movement |
| 47 | Autoplay loop with no pause | Pause on hover/focus; offer an off switch |
| 48 | Relying on fill-mode to hold the end state | Write the end state as static styles |

## 1.2 Full 18-Point AI-Slop Checklist (Slop Audit)

**The statistical-default test**: Swap the logo and product name—does the page still look like "any AI product"? If so, it's slop. The difference isn't taste; it's whether decisions were made.

| # | Signal | Why it's slop | Fix |
| --- | --- | --- | --- |
| 1 | Purple-blue gradient + outer glow | Model statistical default, unrelated to the product | Use a sourced accent color; gradients only for large backgrounds |
| 2 | Four identical-size KPI cards as hierarchy | No protagonist; they compete | One hero number at 3x body size; reshape the rest |
| 3 | Emoji as icons | Inconsistent across platforms, uncontrollable | One icon library, uniform stroke width |
| 4 | One radius/shadow/spacing everywhere | No rhythm | Radius ≤3, shadow ≤2, spacing in three tiers |
| 5 | Gray text on white for body copy | Insufficient contrast | Compute from real hex, ≥4.5:1 |
| 6 | Blue Delete Account | Danger action shares the primary color | Danger color only for danger actions, in both themes |
| 7 | Glassmorphism / bento everywhere | Style overrides content | Glass only for overlays; bento only for truly parallel objects |
| 8 | Glowing orbs / 3D blob hero art | Fits anything | Real screenshots/scenes; no image if none |
| 9 | "Elevate / empower / one-stop / redefine" | Empty words that fit anyone | Write the specific object, action, result |
| 10 | Fake metrics / fake reviews / fake countdowns | Deceptive and obviously fake | Real data; leave an empty state if none |
| 11 | fade+slide-up on every block | Motion became the default style | Only on first entry/level change |
| 12 | Icon soup + small hit areas | Unrecognizable, untappable | Pair icons with text; hit area ≥44 |
| 13 | Hardcoded colors + a palette per page | Themes can't unify | Semantic tokens; one theme, two modes |
| 14 | Heading 24 / body 14 | Only 1.7x, no display hierarchy | Heading 28–32, body 16, span ≥1.75x |
| 15 | Loading looks gray like disabled | States indistinguishable | Keep intensity + spinner |
| 16 | UI text saying "example/diagram/not connected" | Production notes as product copy | Put them in the delivery notes |
| 17 | Card inside card | Hierarchy from stacked containers | One container + spacing groups |
| 18 | Badges/pills/progress rings all over | Decoration disguised as information | Keep only states with meaning |

Fix order is not reversible: **Audit** (list locations + severity) → **Direction** (personality / visual world / 1–2 moments that carry color / signature detail) → **Passes** (spacing → color → icons → copy → type → layout → detail → imagery → motion). If the UI is already good, say so and give only small refinements—don't redo it just to show effort.

**Restraint**: One protagonist per screen; don't turn three parallel items into three identical cards; before adding any decoration ask "what is lost if I delete this?"—if you can't answer, delete it.

## 2. Typography

- Font stacks: iOS `-apple-system, "SF Pro Text"`; Android `Roboto/Noto Sans`; Web `system-ui, -apple-system, "Segoe UI", Roboto, "PingFang SC", "Microsoft YaHei", sans-serif`. Chinese must have an explicit Chinese fallback, or Windows may fall back to SimSun.
- At most two font families per product; monospace only for code, IDs, amounts (or `font-variant-numeric: tabular-nums`).
- Body 16; Chinese line height 1.7–1.8, Latin 1.5; large headings 1.15–1.3; paragraph spacing ≥ half the line height.
- Line length 30–40 Chinese characters, 45–75 Latin characters, `max-width: 65ch`.
- Letter spacing: large Latin headings -0.01 ~ -0.02em; all-caps small labels +0.06 ~ +0.1em; no negative tracking for Chinese.
- Truncation: single line `overflow:hidden; text-overflow:ellipsis; white-space:nowrap` + container `min-width:0`; multiline `-webkit-line-clamp`. Don't truncate important information.
- Chinese line breaking `line-break: strict; overflow-wrap: anywhere;`; headings may use `text-wrap: balance`.
- No italics for Chinese emphasis; at most 3 weights per line; never carry key information below 12px.
- Custom fonts `font-display: swap`, load 400/600 only; prefer system fonts or subsetting for Chinese.
- Four type sizes: 28/600 page title; 20/600 section title; 16/400 body; 13–14/400 auxiliary. Card titles use 16/600.
- Numbers use `tabular-nums` (tables, amounts, counts, countdowns).

## 3. Color

- Semantic tokens first: `--bg` / `--surface` / `--surface-2` / `--border` / `--border-strong` / `--text` / `--text-2` / `--accent` / `--on-accent` / `--accent-soft` / `--accent-soft-text` / `--accent-text` / `--success` / `--success-text` / `--warn` / `--warn-text` / `--danger` / `--danger-text`. Never hardcode hex in components.
- Contrast: body (<18.66px regular / <24px bold) ≥ 4.5:1; large text ≥ 3:1; icons, input borders, focus rings ≥ 3:1. `#9CA3AF` is about 2.8:1 on white and fails; use `#6B7280` (about 4.8:1).
- Color never carries information alone: errors need icon + text; charts need shape/label/texture; up/down needs arrow + sign.
- Accent only for primary actions, selected state, and key numbers, area ≤ 10%; sidebar selection uses `--accent-soft` background + `--accent-soft-text` text, not a solid bar.
- 60-30-10: 60% neutral, 30% secondary, 10% accent; semantic colors don't count against the accent budget.
- Dark mode: background `#0f1115`–`#161a22`, surfaces 4–8% lighter than background; text `#e8ecf4`, never pure white; replace shadows with borders/luminance layering.
- Gradients only for large backgrounds or accent blocks; no low-contrast gradients on text.

### 3.1 Measured Contrast for This Skill's Tokens

Light (surface `#ffffff`):

| Pair | Ratio | Use |
| --- | --- | --- |
| `#141821` / `#ffffff` | 17.76 | Body |
| `#5f6b7f` / `#ffffff` | 5.39 | Auxiliary text |
| `#ffffff` / `#2f5fe0` | 5.48 | Primary button |
| `#2b57d6` / `#e8efff` | 5.30 | Selected state text |
| `#2b57d6` / `#ffffff` | 6.11 | Link/accent text |
| `#ffffff` / `#c93030` | 5.32 | Danger button |
| `#ffffff` / `#157a4d` | 5.35 | Success button |
| `#8a5a00` / `#ffffff` | 5.93 | Warning text |
| `#8b93a3` / `#ffffff` | 3.09 | Control border |

Dark (surface `#161a22`):

| Pair | Ratio | Use |
| --- | --- | --- |
| `#e8ecf4` / `#161a22` | 14.72 | Body |
| `#9aa6ba` / `#161a22` | 7.09 | Auxiliary text |
| `#ffffff` / `#3f6ae0` | 4.83 | Primary button |
| `#cfe0ff` / `#1c2a4d` | 10.59 | Selected state text |
| `#8fb0ff` / `#161a22` | 8.15 | Link/accent text |
| `#ff9a9a` / `#161a22` | 8.58 | Danger text |
| `#6fd6a3` / `#161a22` | 9.81 | Success text |
| `#e0b45a` / `#161a22` | 9.00 | Warning text |
| `#6b7689` / `#161a22` | 3.80 | Control border |

Note: dark `--accent` on `--accent-soft` is only **2.92:1**, so selected state must use `--accent-soft-text`. Recompute every new color with the method above; don't trust your eyes.

## 4. Shape, Borders, and Shadows

- Radius 8 / 12 / 16 / full, ≤ 3 kinds per screen; nested radius = outer radius − padding.
- Border 1px; in dark mode use luminance difference instead of heavy borders.
- Two shadow levels are enough: `0 1px 2px rgb(0 0 0/.06)` (grounded) and `0 8px 24px rgb(0 0 0/.12)` (floating).
- Dividers start at the text's left edge, not full-bleed; if spacing can group it, don't draw a line.
- Focus ring: 2px solid + 2px offset, radius follows the component; don't just set `outline: none`.

## 5. Icons

- One icon library, uniform stroke width (1.5 or 2) and caps; don't mix outline and filled icons on one screen.
- Icon button hit area ≥ 44×44; icon visual size 20–24.
- Icon + text: spacing 6–8; icon-only must have `aria-label` and tooltip.
- Don't use icons for abstract concepts ("collaboration", "intelligence"); use text or a text+icon combo.
- Platform icons follow platform semantics: SF Symbols on iOS, Material Symbols on Android.

## 6. Motion Values (full recipes in `motion.md`)

| Item | Value |
| --- | --- |
| Press | 80ms; `ease-out` |
| Micro-interaction | 120–200ms; enter `ease-out`, exit `ease-in` |
| Overlay/card | 200–280ms; enter `ease-out`, exit `ease-in` |
| In-page movement | 220–320ms; `ease-in-out` |
| Page transition | 300–350ms; `ease-in-out` |
| Theme spread | 400–500ms; `ease-in-out` |
| List stagger | 20–40ms/item, total ≤ 500ms |
| Progress/marquee/spinner | `linear` |
| Spring | 300–450ms; `cubic-bezier(.34,1.56,.64,1)` |
| Cap | UI animation ≤ 400ms; particles ≤ 80 (mobile ≤ 40) |

Three principles: **control duration** (by frequency/distance/area; exits shorter than enters); **prefer high-performance properties** (animate only transform/opacity, use FLIP for layout changes); **respect system settings** (static equivalent for reduce-motion, haptics when available).

## 7. Copy

- Buttons use verb + object: "Save changes", "Delete project", "Send invite"; not "OK/Submit".
- Errors state three things: what happened, why, how to fix. "Email format is invalid, e.g. name@company.com".
- Empty state = one explanation + one primary action; don't ship an illustration alone.
- Destructive confirmation names the object and consequence: "Delete 'Q3 Report'? This cannot be undone."
- One word per concept site-wide; keep a glossary (project/space/member, don't mix).
- Use full-width punctuation for Chinese; space between Chinese and Latin; space between number and unit (12 GB).
- No exclamation-mark emotion stacking; no "dear"/"folks" words that clash with brand voice.

## 8. Accessibility Quick Reference

| Item | Requirement |
| --- | --- |
| Contrast | Body 4.5:1; large text/icons 3:1 |
| Touch | iOS 44×44pt; Android 48×48dp; Web 44×44px |
| Keyboard | Every interaction reachable by Tab; order = visual order; Esc closes overlays |
| Focus | Visible focus ring; focus moves into an opened overlay and returns to the trigger on close |
| Semantics | Buttons use `<button>`; navigation uses `<nav>`; heading levels don't skip |
| Form | Persistent labels; associate errors with fields via `aria-describedby` |
| Icons | Decorative `aria-hidden`; functional get `aria-label` |
| Animated images | Autoplay ≤ 5s or pausable; flashing ≤ 3 times/second |
| Motion | Respect `prefers-reduced-motion`; never lose information to animation |
| Zoom | 200% text doesn't truncate or overlap; no `user-scalable=no` |
| Screen reader | Announce changes with `aria-live="polite"`; don't just say "success" |
| Color | Never convey state by color alone; links can't be distinguished by color alone |

## 9. Review: Evidence and Report Format

**No evidence, no pass.** "Should be fine" is not acceptance; mark what you can't verify as a "limitation", don't disguise it as "pass".

| Item | Evidence | Fails |
| --- | --- | --- |
| Real screens | One screenshot each at 390 / 768 / 1440, reviewed | Reading code only, default state only |
| Contrast | Computed from real hex, covering default/hover/disabled/loading | Eyeballing, body text only |
| Keyboard | Tab through the main flow, focus visible, Esc closes overlays | One mouse click-through |
| Reduced motion | Run the main flow with the system switch on | Media query written but never tested |
| Narrow screens | No horizontal overflow at 280px | Testing 390 only |
| Tokens | No hardcoded colors, theme switch with no per-page palette | Colors scattered in components |
| Semantic color | Delete is the danger color in both themes | Danger action in the primary color |
| State distinction | Loading keeps intensity + spinner, not equal to disabled | Graying out to fake loading |
| Real content | No fake metrics/reviews/countdowns; sample data only in delivery notes | "Example" written in the UI |

Use a fixed format for review output to avoid "feels off":

```text
#### Design review: <name>

### Summary
2–3 sentences. Verdict: Excellent / Good / Needs work / Critical issues.
One sentence on the page's thesis and memorable point, or note that it has none.

### Critical
Must fix: accessibility failures, unusable on narrow screens, broken platform conventions.
- What: problem + values (contrast, size, overflow px)
- Why: matching principle/section
- Fix: concrete change

### Improvements
Should fix, each tagged High / Medium / Low, same format as above.

### Craft notes
Direction, type, signature details, restraint.

### What works
Specific things to keep—write them clearly so the next round doesn't remove them.
```

Severity: **Critical** = accessibility/unusable/convention break; **High** = obvious friction, low contrast, platform mismatch, template feel; **Medium** = suboptimal pattern, inconsistency; **Low** = polish. Write "Critical issues" only when Critical exists; "Excellent" only when there is no Critical and few Highs.

## 10. Delivery Self-Check (Print Version)

- [ ] Content has a unique home, grouped 3–7, group names are nouns
- [ ] Page header: title + one-line description + one primary action
- [ ] Toolbar holds content-area actions only; global actions in the top bar
- [ ] Spacing in three tiers: 4–8 / 16–24 / 32–48
- [ ] Type sizes ≤ 4; one solid primary button per screen
- [ ] All four states: default/empty/loading/error, switching without jump
- [ ] Passes at 390 / 768 / 1440; mobile reflows, not compresses
- [ ] Contrast, touch, keyboard, 200% text pass
- [ ] Motion: ease-out enter, ease-in exit, ease-in-out movement, linear uniform
- [ ] Motion ≤ 400ms; animate transform/opacity only; reduce-motion fallback
- [ ] Semantic tokens, no hardcoded colors; no pure black/white in dark mode
- [ ] Consistent terminology; actionable errors; destructive actions undoable
