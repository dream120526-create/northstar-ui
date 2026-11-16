# DESIGN.md — Design contract

> A one-page design system for agents. Fill it before a new page or redesign; skip it for copy/color/spacing/element-removal edits.
> It answers four questions: who is this for, what is the one-sentence tone, who is the hero on each screen, and what is forbidden.
> Write it before code. If this file and the code disagree, fix one; never let both drift. If it conflicts with an explicit user request, the user wins.
> When the product is not one of the 10 patterns in `SKILL.md` §5, record the shaping questions you asked and the structure you derived here.

## 1. Product facts

- Product / page: `<name>`
- User: `<who, in what context, on what device>`
- One job: `<the one thing the user came here to do>`
- Real content: `<real fields/data/products/copy; write "none yet" if none>`
- Constraints: `<framework, brand assets, components that must stay, performance/offline/permission limits>`
- Reference mode: `<borrow style / redesign / pixel-match — choose one; separate product references from interaction references>`

## 2. One-sentence north star

> `<Product> looks like <a concrete world/object/publication>, because <its most distinctive decision>.`

Passing bar: it tells you whether a button should be round, whether a gradient belongs, whether anything should move.
Failing: "modern, clean, airy, tech-forward" — these cannot make a decision.

## 3. Character and anti-character

- Three character words (specific): `<e.g. restrained, printed, engineered>`
- Three anti-character words (explicitly avoid): `<e.g. neon, glassmorphism, cute illustration>`
- Signature detail (appears once or twice site-wide): `<e.g. 2px rule left of headings / tabular numerals / paper grain>`
- Memory point: the one thing a user remembers after closing the page: `<...>`

## 4. Hero and primary action per screen

| Screen | Hero (one) | Primary action (one) | Secondary (how it is demoted) |
| --- | --- | --- | --- |
| `<Home>` | `<biggest number / main image / title>` | `<create / buy / send>` | `<text button / icon / ··· menu>` |
| `<Detail>` | `<...>` | `<...>` | `<...>` |

Rule: one hero and one filled primary button per screen; merge or demote actions of equal weight.

**Pattern derivation (required when outside the 10 skeletons)**

| Shaping question | User's answer | Effect on structure |
| --- | --- | --- |
| `<Serial or parallel?>` | `<...>` | `<queue / single flow>` |
| `<Retry on failure?>` | `<...>` | `<where errors live>` |
| `<Progress per task or per byte?>` | `<...>` | `<row progress / aggregate progress>` |

## 5. Visual world

- Color strategy: `<neutral + one accent / two-color / monochrome>`; accent only for: `<primary action, selection, key numbers>`
- Accent: `<hex>`; secondary: `<hex>`; danger: `<hex>`; success: `<hex>`
- Dark mode: `<follow system / dark only / light only>`; separate surfaces by luminance, never pure black or pure white.
- Type: `<Latin / CJK / mono>`; headings `<weight>`; body 16; auxiliary 13.
- Imagery: `<real product shots / photography / none / custom illustration>`; forbidden: `<stock photos, glowing orbs, AI illustration>`.
- Material: `<flat / hairline borders / paper / glass (overlays only)>`; one depth strategy per screen.

## 6. Tokens (copy into code)

```css
:root {
  --bg: #f7f8fa; --surface: #ffffff; --surface-2: #f1f3f7;
  --border: #e3e7ee;          /* dividers only; controls use --border-strong */
  --border-strong: #8b93a3;   /* 3.09:1 on surface */
  --text: #141821; --text-2: #5f6b7f;
  --accent: #2f5fe0; --on-accent: #ffffff;      /* 5.48:1 */
  --accent-soft: #e8efff; --accent-soft-text: #2b57d6; /* 5.30:1 */
  --accent-text: #2b57d6;                       /* 6.11:1 on surface */
  --success: #157a4d; --success-text: #0f6b43;  /* white on success 5.35 */
  --warn: #8a5a00; --warn-text: #8a5a00;        /* 5.93:1 on surface */
  --danger: #c93030; --danger-text: #b02a2a;    /* white on danger 5.32 */
  --radius: 12px;
  --space-1: 4px; --space-2: 8px; --space-3: 12px;
  --space-4: 16px; --space-6: 24px; --space-8: 32px; --space-12: 48px;
  --dur-fast: 120ms; --dur-base: 200ms; --dur-slow: 280ms;
  --ease-in: cubic-bezier(.4,0,1,1);
  --ease-out: cubic-bezier(0,0,.2,1);
  --ease-in-out: cubic-bezier(.4,0,.2,1);
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

**Never use `--accent` as text on `--accent-soft`**: in dark mode that pair is only 2.92:1. Use `--accent-soft-text`; filled buttons use `--accent` background + `--on-accent` text. Recompute all text pairs from real hex before delivery; at minimum `text/surface`, `text-2/surface`, `on-accent/accent`, `accent-soft-text/accent-soft`, `danger-text/surface`.

## 7. Component rules

- Buttons: 44–52 tall; one primary per screen; destructive uses danger; loading keeps full strength + spinner, never borrows disabled dimming.
- Inputs: persistent labels; inline errors; 44–52 tall; one column on mobile.
- Cards: only for independent objects; attributes of one object use a grouped list.
- Navigation: 3–5 items on mobile, 5–7 on desktop; noun labels; page-level actions never enter navigation.
- Icons: one library, one stroke width; icon-only controls need a label; hit area ≥44.
- Radius/shadow: ≤3 radii and ≤2 shadow levels per screen; do not use one radius everywhere.

## 8. Motion

- Enter `ease-out`; exit `ease-in`; in-page movement `ease-in-out`; progress/marquee `linear`.
- Press 80–120ms; micro 120–200ms; overlay 200–280ms; page 300–350ms.
- Only `transform`/`opacity`; static equivalent under reduce-motion; every animation interruptible.
- This product uses only these three motions: `<press feedback / list enter / success confirmation>`; nothing else.

## 9. Do / Don't (anti-slop)

**Do**
- Make grayscale structure work before adding color and imagery.
- Use real content, real data, real screenshots; mark sample data in the delivery notes, never in the UI.
- One focal point per screen; give different content different shapes instead of three identical cards.
- Buttons use verb + object; errors say how to fix the problem.

**Don't**
- Purple-blue gradients, neon glow, glassmorphism everywhere, bento everything.
- Emoji as icons; icon soup; four identical KPI cards as "hierarchy".
- One radius, one shadow, one spacing everywhere.
- Low-contrast gray body text; blue "Delete"; loading dimmed like disabled.
- "Elevate your workflow / empower / all-in-one" filler; fake metrics, testimonials, or countdowns.
- Hard-coded colors/spacing; a different palette per page.
- Motion, illustration, or gradients added to look "designed".

## 10. Quality gates (run before delivery)

- [ ] No horizontal overflow at 280px; 390 / 768 / 1440 pass
- [ ] Contrast from real hex: body ≥4.5:1, large text/icons ≥3:1 (including hover/disabled/loading)
- [ ] Keyboard completes the primary flow; focus visible; screen-reader labels complete
- [ ] Touch ≥44 (Web/iOS) / 48 (Android)
- [ ] Static equivalent under `prefers-reduced-motion`
- [ ] One filled primary button; destructive actions use danger in both themes
- [ ] No hard-coded colors; theme switch does not swap per-page palettes
- [ ] Four states: default / empty / loading / error
- [ ] None of the Don't items above appear
- [ ] Looking at a screenshot, you can name whose product it is—not "an AI made this"
