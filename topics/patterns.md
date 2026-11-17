# Page pattern library

> Decide the action the user came to this page to complete (see / find / edit / compare / buy / sign in), then choose the pattern.
> All dimensions use a 390-wide mobile and 1440 desktop baseline; three spacing tiers: within a group 4–8 / between groups 16–24 / between blocks 32–48.

## 1. Admin dashboard

**Answers**: How are things now, where is the problem, what to check next.

```
Sidebar 248 │ Top bar 56
            │ Page header 72: title + time range + export
            │ KPI row: 3–4 cards, tabular numerals 30/600
            │ Chart grid: main chart 8 cols + secondary charts 4 cols
            │ Detail table: 8–10 columns by default, rest in column settings
```

- KPI cards hold only: metric name 13, number 30/600, change 13 (arrow + color + sign). No decorative images.
- The main chart makes one point; secondary charts carry no more than 3 series; legends are clickable to filter.
- With no data, keep the axes and "No data yet"; never drop the whole block.
- Mobile: KPI row scrolls horizontally or 2×2; charts single column; detail table becomes a card list.

## 2. Data category page

**Answers**: Find objects among many of the same kind, by dimension.

- Structure: page header (title + result count) → toolbar (search, filter, sort, view toggle) → facet/category nav → grid → pagination.
- Filters ≤ 5 common ones + "More filters"; show selected filters as chips, each clearable on its own.
- Grid 3–4 columns (desktop), 2 (tablet), 1 (phone); equal card heights.
- When results are empty, offer "Clear filters" instead of just "No results".
- Default sort matches the business goal (relevance / last updated); never default to creation time.

## 3. List + detail

**Answers**: Browse a set, then go deep on one.

- Desktop: list 320–400 on the left + detail on the right; the selected row has a clear selected state (2px primary bar on the left + 8% fill).
- Mobile: two levels; going back preserves list scroll position and filters.
- List row: primary text 16, secondary text 13, trailing meta 13; row height 56–72; the whole row is clickable.
- Detail: page header (back + title + status + primary action) → 3–4 key facts → grouped details → danger zone.
- Switching detail never triggers a full-page loading state; use a local skeleton or keep old content + top progress.

## 4. Chat

**Answers**: Continuous conversation and context.

- Three parts: conversation list (280–320 on desktop) / message stream / input area (fixed to the bottom + safe area).
- Messages max 65ch or 72% wide; distinguish self from other by alignment + background, not color alone.
- Day separators; collapse avatars for consecutive messages from the same person.
- Input supports multiple lines, attachments, and send; Enter-to-send must be configurable (mobile prefers newline).
- When new messages arrive: auto-scroll if the user is already at the bottom, otherwise show a "New messages" button.

## 5. Editor

**Answers**: Long, focused work on one object.

- Top bar: back / file name / save status / primary action; toolbar: undo, redo, formatting; canvas centered with a max width; right properties panel 280–320.
- Canvas background one step darker than the panel, for a "workbench" feel; the panel collapses.
- Autosave needs states (saving / saved / failed, retry); never silent.
- Intercept navigation with unsaved changes; offer "Save and leave / Discard / Cancel".
- Mobile: properties panel becomes a bottom sheet; toolbar scrolls horizontally.

## 6. Settings

**Answers**: Change configuration, scannable and recoverable.

- Page header → 3–5 grouped lists → danger zone (48 gap, danger color).
- Group title 13/400 secondary; row height 56; no dividers inside a group; the whole row is clickable.
- Switches that apply instantly and forms that need "Save" must not share a group.
- Each row: label 16 + current value 13 secondary + chevron/switch; truncate long values with a full tooltip.
- Sign out / delete account sit at the very bottom, 48 away from what's above, with a confirmation that states the consequences.

## 7. E-commerce listing

**Answers**: Compare products and drive the click.

- Structure: search + filter bar (sticky) → product grid 3–4 columns → pagination / load more.
- Card: image 1:1 or 4:5, title 2 lines, price 16–20/600, secondary info 13; "Add to cart" is secondary and must not steal the main click.
- Price, stock, and delivery info must be real; no fake countdowns.
- Filter panel is a bottom sheet on mobile; after applying, show the result count and "Clear".
- Lazy-load images with fixed-ratio placeholders to prevent layout shift.

## 8. Authentication page

**Answers**: One task: sign in / sign up.

- Structure: brand → title + one line of explanation → 2–3 fields → primary button → third-party / secondary links.
- No full navigation; one primary button per screen; "Forgot password" is a text link, not equal in weight to the primary button.
- Fields: email/phone, password (with visibility toggle), verification code; errors show inline, never as a toast.
- Third-party sign-in sits below the main form, separated by "or"; follow each platform's button specs.
- On mobile, keep the primary button visible when the keyboard opens; support password managers and autofill.

## 9. Marketing page

**Answers**: Persuade and drive one action.

- Z-shaped flow: brand top-left → secondary top-right → diagonal hero → CTA bottom-right.
- The first screen must answer: what this is, who it's for, why trust it, what to do next.
- 3–4 value points, each a one-line title + one line of explanation; social proof uses real logos / data / reviews.
- Repeat the CTA, but one action, one wording; the first-screen primary CTA sits bottom-right or at the end of the form.
- Restrained motion: first-screen entrance and scroll reveals only; never autoplay video/audio.

## 10. Result / empty state page

**Answers**: What happened, where to go next.

- Structure: icon/illustration → title (the result) → one line of explanation → one primary + one secondary action.
- Success pages offer a next step (view order / back to list); never just "Success".
- Distinguish empty states: first-time empty (guide creation), filtered empty (clear conditions), error empty (retry).
- 404/500 pages keep navigation and search; never just an illustration.

## 11. Data-dense pages, continued

- Tables: row height 40–48; sticky header; numbers right-aligned, text left-aligned; zebra stripes optional, but must not clash with hover.
- Keep ≤ 7 columns visible, the rest go to "Column settings"; pin the first column; keep it and the header in place while scrolling horizontally.
- Filter bar: search + 2–3 high-frequency filters + "More"; selected conditions as clearable chips.
- Bulk actions: a floating action bar appears on selection, showing the count and "Deselect".
- Charts: one chart, one point; ≤ 5 colors; provide a text summary/data table; annotate axes that don't start at zero.
- Prefer pagination over infinite scroll; infinite scroll must have "You're all caught up" and back-to-top.

## 11.5 Beyond the 10 categories: deriving a new pattern

First list the content inventory (one line each, labeled "what question it answers"), then ask the 2–3 questions that decide the pattern:

| Question | Decides |
| --- | --- |
| Is the work serial or parallel? | Single-column flow page vs queue/board |
| Should failures retry automatically or need a human? | State-machine complexity, number of error levels |
| Is progress per task or per byte? | Per-row progress vs aggregate progress bar |
| Does it stay open long-term or is it used and closed? | Workspace shell vs single-page overlay |
| Are the core objects hierarchical or a graph? | Tree/detail page vs relationship canvas |

Then fill the five zones: whose navigation, what the page header says, what the toolbar does, what the content area shows, what the footer completes; write "the hero / primary action per screen" into `DESIGN.md`.

**Example (downloader)**: parallel tasks → queue; failures retryable → inline error + retry; progress per byte → thin per-row progress bar + aggregate total; stays open long-term → sidebar (categories) + list + bottom action bar (pause all / clear). None of it came from "the most recent skeleton".

**Specific acceptance**

- **Task type** (download / import / batch): every task has queued/running/failed/done; can pause, retry, and see why it failed; one progress unit throughout; concurrency is capped.
- **Transaction type** (checkout / transfer / trade): amount, currency, and fees match on every page; submission is idempotent; irreversible actions confirm the object + amount; success returns a receipt number.
- **Both**: state recovers after refresh/re-entry; never fake progress; failures never lose user input.
- **Content-flow type** (feed / messages / comments): scroll position survives back-navigation; new items never jump the list under the reader; pagination failure shows a retry row, not an empty list.

## 12. Two Before / After examples

**List page Before**: a 12-column table dumped under the title, with search/filter/export/new crammed into the title row, row height 72, all columns equal width.
**After**: page header (title + new) → toolbar (search, status, time, export) → table (pinned first column, right-aligned numbers, row height 44) → pagination; a card list on mobile.

**Detail page Before**: 20 fields flattened, 5 equal-level buttons, the primary action on the 4th screen.
**After**: page header (back + title + status + edit) → 3 key facts → three groups: basics/configuration/activity → danger zone; the primary action always lives in the page header.
