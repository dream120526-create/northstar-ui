# State design: seven states, feedback, and optimistic updates

> Users don't only see the "has data" version. Design at least seven states per container, all in the same container, with no jump when switching.

## 1. The seven states

| State | What users need to know | What the page gives |
| --- | --- | --- |
| Default | What's here | Real content + primary action |
| Empty (first time) | Why it's empty, how to start | One line of explanation + one create action |
| Empty (filtered) | Filters removed everything, it isn't gone | Result count 0 + clear filters |
| Loading | Something is happening, it isn't frozen | Skeleton / local spinner; appears only after > 1s |
| Error | What happened, can it retry | Inline error + retry; keep what was entered |
| Offline | It's the network, not the product | Top bar + the offline capabilities that still work |
| Partial success | Which succeeded, which failed | Per-item status + retry the failed ones |

Also: insufficient permissions (say what's missing, who to ask), limit reached (show quota and upgrade path), expired (refresh / sign in again).

## 2. Empty states

```
[Icon 48–64]
Title 20/600: No projects yet
Description 14: Projects organize your boards and reports.
[Primary: New project]   [Secondary: View sample]
```

- Distinguish first-time empty, filtered empty, and error empty; copy and actions differ.
- Empty states occupy the content area; don't jump to a new page; never an illustration alone.
- Keep the page header and toolbar when a list is empty, or users won't know how to recover.

## 3. Loading

- First screen: skeleton shape = the real layout (same row count, column widths, card ratios), never a full-screen spinner.
- Local: button loading (fixed width + "Saving"), list footer loading, chart overlay.
- Delayed appearance: loads under 300ms show no loading at all, to avoid flicker; over 1s show skeleton/progress.
- Optimistic updates: change the UI first, then request; roll back on failure with one actionable message.
- Prevent duplicate submits: disable or ignore repeat clicks during the request, but keep the button visible.
- Use a progress bar when progress is determinable (upload/import); a spinner only when it isn't.

## 4. Errors

- Inline errors beat toasts; for critical errors (save failed, payment failed) use a banner or page-level message.
- Copy: what happened + why + what to do; offer "Retry" and "Back".
- Never clear the form on a network error; keep user input.
- Bulk failures: list the failed items (expandable) and support "Retry failed items".
- Never rely on red alone: icon + copy + border/background.

## 5. Offline and weak networks

- Persistent top bar: "You're offline. Changes will sync when you're back." It disappears and sync resumes automatically.
- State which features work offline (drafts, cached content); disable unavailable buttons with an explanation.
- Request timeout 10–15s; retry with backoff (1s/2s/4s); never spin forever.
- Provide placeholders for failed images/fonts; one failed asset must not blank the page.

## 6. Choosing feedback components

| Need | Use | Don't use |
| --- | --- | --- |
| Action succeeded, no decision needed | Light toast / button success state (1.5–2s) | Modal |
| Confirmation required | Dialog: verb + object + consequence | Toast |
| Critical error | Inline error / page banner | Auto-dismissing toast |
| Background progress | Top progress bar / local status | Full-screen overlay |
| Undoable action | Toast + undo (5–8s) | Confirmation prompt |
| Field error | Copy + icon below the field | Summary dialog |

## 7. Confirmation and undo

- Prefer "undoable" over "confirm first" for destructive actions (delete → undo toast for 8s).
- When confirmation is required, the button states the verb: "Delete 3 projects", not "OK".
- Danger buttons use the danger color but never take focus by default; Esc cancels.
- Bulk delete shows the count and irreversibility; high-risk actions require typing the name to confirm.

## 8. First-run guidance

- Show once only; skippable and replayable; never on every sign-in.
- Point at real controls, not fake screenshots; one point per step (≤ 3 steps).
- The empty state is the best onboarding: explanation + example + primary action.
- New features get a one-time badge/tip that disappears permanently once used.

## 9. State acceptance

- [ ] Default/empty/loading/error share one container, with no jump when switching
- [ ] Empty states distinguish first-time/filtered/error, and include a primary action
- [ ] Loading appears only after 300ms and matches the real layout
- [ ] Errors are inline, actionable, and preserve input
- [ ] Offline is announced and syncs automatically on recovery
- [ ] Failed optimistic updates roll back
- [ ] Feedback components are chosen correctly; never toast a critical error
- [ ] State changes are explained by motion, yet no information is lost under reduced motion
