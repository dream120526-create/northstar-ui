# Code Recipes: Tokens, Shell, Dialog, Toast, Skeleton

> After copying, replace `--accent` and the font stack, then tune to the brand. All recipes include accessibility and reduce-motion by default.

## 1. Design Tokens

```css
:root {
  --bg: #f7f8fa;
  --surface: #ffffff;
  --surface-2: #f1f3f7;
  --border: #e3e7ee;          /* dividers only */
  --border-strong: #8b93a3;   /* control boundary; 3.09:1 on surface */
  --text: #141821;
  --text-2: #5f6b7f;
  --accent: #2f5fe0;          /* white text 5.48:1 */
  --on-accent: #ffffff;
  --accent-soft: #e8efff;
  --accent-soft-text: #2b57d6; /* 5.30:1 on accent-soft */
  --accent-text: #2b57d6;      /* 6.11:1 on surface */
  --success: #157a4d;          /* white text 5.35:1 */
  --success-text: #0f6b43;
  --warn: #8a5a00;             /* white text 5.93:1 */
  --warn-text: #8a5a00;
  --danger: #c93030;           /* white text 5.32:1 */
  --danger-text: #b02a2a;
  --radius: 12px;
  /* Spacing: all full-page recipes reference these 7; a missing one silently breaks styles */
  --space-1: 4px;  --space-2: 8px;  --space-3: 12px; --space-4: 16px;
  --space-6: 24px; --space-8: 32px; --space-12: 48px;
  --shadow-1: 0 1px 2px rgb(0 0 0 / .06);
  --shadow-2: 0 8px 24px rgb(0 0 0 / .12);
  --font: system-ui, -apple-system, "Segoe UI", Roboto, "PingFang SC",
          "Microsoft YaHei", sans-serif;
  /* Motion: buttons/overlays/pages all use these; don't write custom durations in components */
  --dur-press: 80ms;  --dur-fast: 120ms; --dur-base: 200ms;
  --dur-slow: 280ms;  --dur-page: 320ms; --dur-exit: 160ms;
  --ease-in: cubic-bezier(.4,0,1,1);
  --ease-out: cubic-bezier(0,0,.2,1);
  --ease-in-out: cubic-bezier(.4,0,.2,1);
  --ease-spring: cubic-bezier(.34,1.56,.64,1);
}
html[data-theme="dark"] {
  --bg: #0f1115; --surface: #161a22; --surface-2: #1e2430;
  --border: #2a3140; --border-strong: #6b7689; /* 3.80:1 on surface */
  --text: #e8ecf4; --text-2: #9aa6ba;
  --accent: #3f6ae0; --on-accent: #ffffff;      /* 4.83:1 */
  --accent-soft: #1c2a4d; --accent-soft-text: #cfe0ff; /* 10.59:1 */
  --accent-text: #8fb0ff;                       /* 8.15:1 on surface */
  --success: #157a4d; --success-text: #6fd6a3;
  --warn: #8a5a00; --warn-text: #e0b45a;
  --danger: #c93030; --danger-text: #ff9a9a;
}
```

Selected state uses `--accent-soft-text`; don't put `--accent` on `--accent-soft` (only 2.92:1 in dark). For contrast values see `reference.md` §3.1.

```css
.sr-only { position: absolute; width: 1px; height: 1px; padding: 0; margin: -1px;
  overflow: hidden; clip: rect(0 0 0 0); white-space: nowrap; border: 0; }
```

## 2. App Shell

```html
<div class="shell">
  <aside class="sidebar" aria-label="Main navigation">
    <div class="brand">Product name</div>
    <nav>…</nav>
  </aside>
  <header class="topbar">…</header>
  <main class="main">
    <div class="page-head">
      <h1>Page title</h1>
      <p>One-line description</p>
      <button class="btn btn--primary">Primary action</button>
    </div>
    <div class="toolbar">…</div>
    <section class="content">…</section>
  </main>
</div>
```

```css
.shell { display: grid; grid-template-columns: 248px 1fr; grid-template-rows: 56px 1fr;
  min-height: 100dvh; background: var(--bg); color: var(--text); font-family: var(--font); }
.sidebar { grid-row: 1 / -1; background: var(--surface); border-inline-end: 1px solid var(--border); }
.topbar { display: flex; align-items: center; justify-content: flex-end;
  padding: 0 24px; background: var(--surface); border-bottom: 1px solid var(--border); }
.main { padding: 24px; min-width: 0; }
.page-head { display: flex; align-items: end; gap: 16px; max-width: 1280px; margin: 0 auto 24px; }
.page-head h1 { font-size: 28px; font-weight: 600; margin: 0; }
.page-head p { color: var(--text-2); font-size: 13px; margin: 0; }
.content { max-width: 1280px; margin: 0 auto; }
@media (max-width: 900px) {
  .shell { grid-template-columns: 1fr; grid-template-rows: 56px 1fr; }
  .sidebar { display: none; }
  .main { padding: 16px; }
  .page-head h1 { font-size: 28px; }
}
```

## 3. Buttons

```css
.btn { height: 44px; padding: 0 16px; border-radius: 10px; border: 1px solid var(--border);
  background: var(--surface); color: var(--text); font: 600 16px/1 var(--font);
  cursor: pointer; transition: transform var(--dur-press) var(--ease-out),
  background-color var(--dur-fast) var(--ease-out); }
.btn:active { transform: scale(.97); }
.btn:focus-visible { outline: 2px solid var(--accent); outline-offset: 2px; }
.btn--primary { background: var(--accent); border-color: transparent; color: var(--on-accent); }
.btn--danger { background: var(--danger); border-color: transparent; color: var(--on-accent); }
.btn--ghost { background: transparent; border-color: transparent; color: var(--text-2); }
.btn--ghost:hover { background: var(--surface-2); color: var(--text); }
.btn--ghost:disabled { opacity: .45; }
.btn:disabled { opacity: .5; cursor: not-allowed; }
```

## 4. Dialog

```html
<dialog class="dialog" aria-labelledby="dlg-title">
  <h2 id="dlg-title">Delete project?</h2>
  <p>"Q3 Report" will be permanently deleted. This cannot be undone.</p>
  <div class="dialog__actions">
    <button class="btn" data-close>Cancel</button>
    <button class="btn btn--danger">Delete project</button>
  </div>
</dialog>
```

```css
.dialog { width: min(440px, calc(100vw - 32px)); border: 0; border-radius: 16px;
  padding: 24px; background: var(--surface); color: var(--text); box-shadow: var(--shadow-2); }
.dialog::backdrop { background: rgb(0 0 0 / .45); }
.dialog__actions { display: flex; justify-content: flex-end; gap: 8px; margin-top: 24px; }
.dialog[open] { animation: dialog-in var(--dur-slow) var(--ease-out); }
@keyframes dialog-in { from { opacity: 0; transform: translateY(8px) scale(.98); } }
@media (prefers-reduced-motion: reduce) { .dialog[open] { animation: none; } }
```

Key points: native `<dialog>` gives you focus trapping and Esc; on close, focus returns to the trigger button.

## 5. Toast

```css
.toast { position: fixed; left: 50%; bottom: 24px; translate: -50% 12px;
  opacity: 0; pointer-events: none; display: flex; align-items: center; gap: 12px;
  padding: 12px 16px; border-radius: 10px; background: #11151d; color: #fff;
  font: 500 13px/1.4 var(--font); box-shadow: var(--shadow-2);
  transition: opacity var(--dur-base) var(--ease-out), translate var(--dur-base) var(--ease-out); }
.toast.is-on { opacity: 1; translate: -50% 0; pointer-events: auto; }
@media (prefers-reduced-motion: reduce) {
  .toast { transition: opacity 80ms linear; translate: -50% 0; }
}
```

Toast is for "success/light hints" only; use in-place errors for critical failures. Keep undoable toasts for 8s.

## 6. Skeleton

```css
.skeleton { background: linear-gradient(90deg, var(--surface-2) 25%,
  var(--border) 37%, var(--surface-2) 63%); background-size: 400% 100%;
  animation: shimmer 1.4s linear infinite; border-radius: 8px; }
@keyframes shimmer { to { background-position: -200% 0; } }
@media (prefers-reduced-motion: reduce) { .skeleton { animation: none; } }
```

Skeleton shape = real layout: same row count, column widths, radius, spacing; don't replace a whole page with one gray block.

## 7. SwiftUI Skeleton

```swift
struct PageHead: View {
    let title: String
    let subtitle: String
    var body: some View {
        HStack(alignment: .lastTextBaseline, spacing: 16) {
            VStack(alignment: .leading, spacing: 4) {
                Text(title).font(.system(size: 28, weight: .semibold))
                Text(subtitle).font(.system(size: 13)).foregroundStyle(.secondary)
            }
            Spacer()
            Button("New") {}.buttonStyle(.borderedProminent)
        }
        .padding(.horizontal, 20)
    }
}

@Environment(\.accessibilityReduceMotion) private var reduceMotion
withAnimation(reduceMotion ? nil : .easeOut(duration: 0.2)) { isOn.toggle() }
```

## 8. Compose Skeleton

```kotlin
@Composable
fun PageHead(title: String, subtitle: String, onPrimary: () -> Unit) {
    Row(
        modifier = Modifier.fillMaxWidth().padding(horizontal = 20.dp),
        verticalAlignment = Alignment.Bottom,
        horizontalArrangement = Arrangement.spacedBy(16.dp)
    ) {
        Column(Modifier.weight(1f)) {
            Text(title, fontSize = 28.sp, fontWeight = FontWeight.SemiBold)
            Text(subtitle, fontSize = 13.sp, color = MaterialTheme.colorScheme.onSurfaceVariant)
        }
        Button(onClick = onPrimary) { Text("New") }
    }
}

val reduceMotion = LocalMotionDurationScale.current.scaleFactor == 0f
val alpha by animateFloatAsState(
    targetValue = if (visible) 1f else 0f,
    animationSpec = if (reduceMotion) snap() else tween(200, easing = LinearOutSlowInEasing),
    label = "alpha"
)
```

## 9. Full List Page Recipe (page header + toolbar + list + pagination)

```html
<div class="list-page">
  <header class="page-head">
    <div>
      <h1>Projects</h1>
      <p>24 projects, 3 pending</p>
    </div>
    <div class="page-head__actions">
      <button class="btn">Import</button>
      <button class="btn btn--primary">New project</button>
    </div>
  </header>

  <div class="toolbar" role="search">
    <label class="search">
      <span class="sr-only">Search projects</span>
      <input type="search" placeholder="Search project or owner">
    </label>
    <button class="chip is-on" aria-pressed="true">In progress</button>
    <button class="chip" aria-pressed="false">Archived</button>
    <span class="toolbar__spacer"></span>
    <button class="btn btn--ghost">Export</button>
  </div>

  <div class="list" role="list">
    <div class="list__head" aria-hidden="true">
      <span>Project</span><span>Status</span><span>Owner</span><span>Updated</span><span></span>
    </div>
    <div class="list__row" role="listitem">
      <a class="row-main" href="#">
        <span class="row-title">Q3 growth dashboard</span>
        <span class="row-sub">12 metrics · updated 2 hours ago</span>
      </a>
      <span class="badge badge--progress">In progress</span>
      <span class="row-meta">Lin Xiaoman</span>
      <span class="row-meta">11/16</span>
      <button class="icon-btn" aria-label="More actions">···</button>
    </div>
    <!-- Repeat .list__row for remaining rows -->
  </div>

  <footer class="pager">
    <span class="pager__count">1–20 / 24</span>
    <div class="pager__actions">
      <button class="btn btn--ghost" disabled>Previous</button>
      <button class="btn btn--ghost">Next</button>
    </div>
  </footer>
</div>
```

```css
.list-page { max-width: 1280px; margin-inline: auto; }
.page-head { display: flex; align-items: flex-end; gap: var(--space-4);
  margin-block-end: var(--space-6); }
.page-head h1 { margin: 0; font-size: 28px; line-height: 1.2; font-weight: 600; }
.page-head p { margin: 4px 0 0; color: var(--text-2); font-size: 13px; }
.page-head__actions { margin-inline-start: auto; display: flex; gap: var(--space-2); }

.toolbar { display: flex; align-items: center; gap: var(--space-2);
  padding-block: var(--space-3); border-block-end: 1px solid var(--border);
  margin-block-end: var(--space-2); }
.toolbar__spacer { margin-inline-start: auto; }
.search input { height: 40px; width: min(320px, 40vw); padding-inline: var(--space-3);
  border: 1px solid var(--border-strong); border-radius: 10px;
  background: var(--surface); color: var(--text); font-size: 16px; }
.search input:focus-visible { outline: 2px solid var(--accent); outline-offset: 2px; }
.chip { height: 44px; padding-inline: 16px; border-radius: 999px;
  border: 1px solid var(--border-strong); background: var(--surface);
  color: var(--text-2); font-size: 13px; cursor: pointer; }
.chip.is-on { background: var(--accent-soft); border-color: transparent;
  color: var(--accent-soft-text); font-weight: 600; }

.list { border: 1px solid var(--border); border-radius: var(--radius);
  background: var(--surface); overflow: hidden; }
.list__head, .list__row {
  display: grid; grid-template-columns: minmax(0, 1fr) 104px 88px 72px 40px;
  align-items: center; gap: var(--space-4); padding-inline: var(--space-4);
}
.list__head { height: 40px; color: var(--text-2); font-size: 13px;
  background: var(--surface-2); }
.list__row { min-height: 64px; color: inherit; text-decoration: none;
  border-block-start: 1px solid var(--border); }
.list__row:hover { background: var(--surface-2); }
.list__row:focus-visible { outline: 2px solid var(--accent); outline-offset: -2px; }
.row-main { display: flex; flex-direction: column; gap: 2px; min-width: 0;
  color: inherit; text-decoration: none; }
.row-title { font-size: 16px; font-weight: 600; overflow: hidden;
  text-overflow: ellipsis; white-space: nowrap; }
.row-sub { font-size: 13px; color: var(--text-2); }
.row-meta { font-size: 13px; color: var(--text-2); font-variant-numeric: tabular-nums; }
.badge { justify-self: start; padding: 2px 8px; border-radius: 999px;
  font-size: 13px; font-weight: 600; }
.badge--progress { background: var(--accent-soft); color: var(--accent-soft-text); }
.icon-btn { width: 40px; height: 40px; border: 0; border-radius: 8px;
  background: transparent; color: var(--text-2); cursor: pointer; }
.icon-btn:hover { background: var(--surface-2); }

.pager { display: flex; align-items: center; justify-content: space-between;
  padding-block: var(--space-4); color: var(--text-2); font-size: 13px; }
.pager__actions { display: flex; gap: var(--space-2); }

@media (max-width: 720px) {
  .page-head { align-items: flex-start; flex-direction: column; }
  .page-head__actions { margin-inline-start: 0; width: 100%; }
  .page-head__actions .btn { flex: 1; }
  .toolbar { flex-wrap: wrap; }
  .search, .search input { width: 100%; }
  .list__head { display: none; }
  .list__row { grid-template-columns: 1fr auto; gap: 4px var(--space-3);
    padding-block: var(--space-3); }
  .row-main { grid-column: 1 / -1; }
  .badge, .row-meta { justify-self: start; }
  .icon-btn { grid-row: 1; grid-column: 2; }
}
```

Data table (same tokens, numbers right-aligned, sticky header):

```css
.table-wrap { overflow: auto; border: 1px solid var(--border);
  border-radius: var(--radius); background: var(--surface); }
.data-table { width: 100%; border-collapse: collapse; font-size: 13px; }
.data-table th, .data-table td { padding: 10px 16px; text-align: start;
  border-block-end: 1px solid var(--border); white-space: nowrap; }
.data-table thead th { position: sticky; top: 0; z-index: 1;
  background: var(--surface-2); color: var(--text-2); font-size: 13px; font-weight: 600; }
.data-table td.num { text-align: end; font-variant-numeric: tabular-nums; }
.data-table tbody tr:hover { background: var(--surface-2); }
```

## 10. Full Settings Page Recipe (grouped list + switch + danger zone)

```html
<div class="settings">
  <header class="page-head"><div><h1>Settings</h1><p>Account, notifications, and data</p></div></header>

  <section class="group" aria-labelledby="g-account">
    <h2 id="g-account" class="group__title">Account</h2>
    <div class="setting-row">
      <span class="setting-row__label">Avatar</span>
      <span class="setting-row__value">Lin Xiaoman</span>
      <span class="chev" aria-hidden="true">›</span>
    </div>
    <div class="setting-row">
      <span class="setting-row__label">Email</span>
      <span class="setting-row__value">lin@example.com</span>
      <span class="chev" aria-hidden="true">›</span>
    </div>
  </section>

  <section class="group" aria-labelledby="g-notify">
    <h2 id="g-notify" class="group__title">Notifications</h2>
    <label class="setting-row">
      <span class="setting-row__label">Push notifications</span>
      <span class="setting-row__value">Important updates and mentions</span>
      <input class="switch" type="checkbox" checked>
    </label>
    <label class="setting-row">
      <span class="setting-row__label">Weekly summary</span>
      <span class="setting-row__value">Mondays 9:00</span>
      <input class="switch" type="checkbox">
    </label>
  </section>

  <section class="group group--danger" aria-labelledby="g-danger">
    <h2 id="g-danger" class="group__title">Danger zone</h2>
    <div class="setting-row">
      <span class="setting-row__label">Sign out</span>
      <button class="btn btn--danger-ghost">Sign out</button>
    </div>
  </section>
</div>
```

```css
.settings { max-width: 720px; margin-inline: auto; }
.group { padding-block: var(--space-2) var(--space-6);
  border-block-end: 1px solid var(--border); }
.group + .group { margin-block-start: var(--space-6); }
.group:last-child { border-block-end: 0; }
.group__title { margin: 0 0 var(--space-2); color: var(--text-2);
  font-size: 13px; font-weight: 600; }
.group--danger { margin-block-start: var(--space-12); }

.setting-row { display: grid; grid-template-columns: 1fr auto 20px;
  align-items: center; gap: var(--space-3); min-height: 56px;
  padding-inline: var(--space-2); border-radius: 8px; }
label.setting-row { cursor: pointer; }
.setting-row:hover { background: var(--surface-2); }
.setting-row__label { font-size: 16px; }
.setting-row__value { color: var(--text-2); font-size: 13px; }
.chev { color: var(--text-2); font-size: 20px; line-height: 1; }

.switch { appearance: none; width: 44px; height: 26px; margin: 0;
  border-radius: 999px; background: var(--border-strong);
  position: relative; cursor: pointer; transition: background var(--dur-fast) var(--ease-out); }
.switch::after { content: ""; position: absolute; inset-block-start: 3px;
  inset-inline-start: 3px; width: 20px; height: 20px; border-radius: 50%;
  background: #fff; transition: transform var(--dur-base) var(--ease-in-out); }
.switch:checked { background: var(--accent); }
.switch:checked::after { transform: translateX(18px); }
.switch:focus-visible { outline: 2px solid var(--accent); outline-offset: 3px; }

.btn--danger-ghost { color: var(--danger-text); background: transparent;
  border-color: var(--danger-text); }
.btn--danger-ghost:hover { background: color-mix(in srgb, var(--danger) 8%, transparent); }

@media (max-width: 520px) {
  .setting-row { grid-template-columns: 1fr auto; }
  .setting-row__value { grid-column: 1; grid-row: 2; }
  .chev { grid-column: 2; grid-row: 1; }
}
```

## 11. Full Form Page Recipe (grouped form + validation + sticky footer action bar)

```html
<form class="form-page" novalidate>
  <header class="page-head"><div><h1>New project</h1><p>You can change this anytime after creating</p></div></header>

  <section class="form-section">
    <h2>Basics</h2>
    <div class="form-grid">
      <div class="field">
        <label for="name">Project name</label>
        <input id="name" name="name" required aria-describedby="name-hint">
        <p id="name-hint" class="field__hint">Used in lists and notifications, 40 characters max</p>
      </div>
      <div class="field">
        <label for="owner">Owner</label>
        <select id="owner" name="owner"><option>Lin Xiaoman</option></select>
      </div>
      <div class="field field--full">
        <label for="desc">Description</label>
        <textarea id="desc" rows="3"></textarea>
      </div>
    </div>
  </section>

  <section class="form-section">
    <h2>Visibility</h2>
    <div class="field field--error">
      <label for="team">Team</label>
      <select id="team" aria-invalid="true" aria-describedby="team-error"></select>
      <p id="team-error" class="field__error">Select a team</p>
    </div>
  </section>

  <footer class="form-footer">
    <button type="button" class="btn">Cancel</button>
    <button type="submit" class="btn btn--primary">Create project</button>
  </footer>
</form>
```

```css
.form-page { max-width: 760px; margin-inline: auto; padding-block-end: 96px; }
.form-section { padding-block: var(--space-6); border-block-end: 1px solid var(--border); }
.form-section h2 { margin: 0 0 var(--space-4); font-size: 20px; font-weight: 600; }
.form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: var(--space-4); }
.field { display: flex; flex-direction: column; gap: 6px; min-width: 0; }
.field--full { grid-column: 1 / -1; }
.field label { font-size: 13px; font-weight: 600; color: var(--text); }
.field input, .field select, .field textarea {
  width: 100%; min-height: 44px; padding: 10px 12px;
  border: 1px solid var(--border-strong); border-radius: 10px;
  background: var(--surface); color: var(--text); font: 400 16px/1.5 var(--font);
}
.field textarea { resize: vertical; }
.field input:focus-visible, .field select:focus-visible,
.field textarea:focus-visible { outline: 2px solid var(--accent); outline-offset: 2px; }
.field__hint { margin: 0; color: var(--text-2); font-size: 13px; }
.field--error input, .field--error select { border-color: var(--danger-text); }
.field__error { margin: 0; color: var(--danger-text); font-size: 13px;
  display: flex; gap: 6px; align-items: center; }
.field__error::before { content: "!"; display: inline-grid; place-items: center;
  width: 16px; height: 16px; border-radius: 50%; background: var(--danger);
  color: var(--on-accent); font-size: 13px; font-weight: 700; }

.form-footer { position: sticky; inset-block-end: 0; display: flex;
  justify-content: flex-end; gap: var(--space-2); padding: var(--space-4) 0;
  margin-block-start: var(--space-6); background: var(--bg);
  border-block-start: 1px solid var(--border); }

@media (max-width: 640px) {
  .form-grid { grid-template-columns: 1fr; }
  .field--full { grid-column: auto; }
  .form-footer { justify-content: stretch; }
  .form-footer .btn { flex: 1; }
}
```

## 12. Recipe Application Order

1. Apply tokens and shell first; confirm 390 / 768 / 1440 all hold.
2. Then fill in page header, toolbar, content area, footer action bar.
3. Add the four states (default/empty/loading/error).
4. Finally add speed: press 80ms, enter ease-out, exit ease-in, movement ease-in-out.
5. Run the P0 checklist and the `motion.md` §7 motion self-check.
