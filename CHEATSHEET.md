# Quiet Axis — cheat sheet

## 1. Change tier
| Change | Do |
| --- | --- |
| Copy / color / spacing / remove element | Edit directly; check contrast and tokens (P0 9, 14) |
| New page | Inventory + MECE → five zones → P0 3/5/6/7/11/22 (+23–25 if motion) |
| New product / redesign | Seven steps + DESIGN.md + slop audit + all 25 P0 |

## 2. Seven steps (new page / redesign)
`DESIGN.md 5 fields → inventory → MECE 3–7 groups → five zones → wireframe → grid/spacing → four states + 3 screenshots + P0`
Direction: **looks like `<a concrete world>`, because `<one executable decision>`**.

## 3. Defaults
| Item | Value |
| --- | --- |
| Spacing | 4 base; 4–8 / 16–24 / 32–48 |
| Type | 28 / 20 / 16 / 13 (four only) |
| Measure | CJK 30–40 chars; `65ch`; ≤1280 |
| Radius | 8 / 12 / 16 / full; ≤3 |
| Touch | iOS 44 / Android 48 / Web 44 |
| Contrast | Body 4.5:1; large text/icons 3:1 |
| Accent | ≤10%; one filled primary per screen |

## 4. Motion
| Motion | Curve | Duration |
| --- | --- | --- |
| Constant speed | `linear` | by cycle |
| Exit | `ease-in` | enter ×0.7 |
| Enter | `ease-out` | 180–260ms |
| In-page A→B | `ease-in-out` | 220–320ms |
| Spring (feedback) | `.34,1.56,.64,1` | 300–450ms |

Only `transform`/`opacity`; ≤400ms; static equivalent under reduce-motion.

## 5. Beyond the 10 patterns
Don't force a skeleton → inventory → ask 2–3 shaping questions (serial or parallel? retry on failure? progress per task or byte?) → derive with five zones → record in DESIGN.md.

## 6. Tokens (measured)
Light: `#2f5fe0` white 5.48 · `#c93030` 5.32 · `#157a4d` 5.35 · `#2b57d6` on `#e8efff` 5.30.
Dark: `#3f6ae0` 4.83 · `#cfe0ff` on `#1c2a4d` 10.59. Never `--accent` on `--accent-soft`.

## 7. Slop top 5
Purple-blue gradient · four identical KPI cards · emoji icons · one radius everywhere · fake metrics.

## 8. Last 30 seconds
`390 screenshot → 5 contrast pairs → Tab through → rerun reduce-motion → 280px overflow`
No screenshot / no hex / no keyboard run = not accepted.

## 9. Gestures & corners
Spring, not fake spring: `mass 1.0 / stiffness 100 / damping 0.8`; inherit release velocity; interruptible; edge = rubber band (`0.25`). Corners: one family, circular **or** squircle. Ask before Liquid Glass → DESIGN.md §8.1.
