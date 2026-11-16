# Platform adaptation: iOS, Android, Web

> The same logic, implemented with each platform's own conventions. Never transplant one platform's controls onto another.

## 1. Platform baseline

| Item | iOS | Android | Web |
| --- | --- | --- | --- |
| Minimum touch target | 44×44pt | 48×48dp | 44×44px |
| Body text size | 17pt (secondary 15) | 14–16sp | 16px |
| Page padding | 16–20pt | 16dp | 16–24px |
| Primary button | Height 50, radius 12–14 | Height 48–56, radius full / 12 | Height 44–52, radius 8–12 |
| Navigation | Bottom tab / large title / back gesture | Top app bar + bottom navigation bar | Sidebar / top bar / breadcrumbs |
| Icons | SF Symbols | Material Symbols | One shared icon set |
| Default motion | Springy, continuous | Material motion | Restrained CSS transitions |

## 2. iOS conventions

- Navigation bar: large title collapses on scroll; back is a chevron + gesture, not a "Back" label.
- Tab bar ≤ 5 items; selected state uses the primary color, not a full-width background.
- Prefer sheets for overlays (dismissable by pulling down); destructive actions use an action sheet.
- Use system switches, segmented controls, and date pickers; custom ones need a reason.
- Safe area: `safeAreaInsets`; a fixed bottom action bar leaves room for the home indicator.
- Motion: `UIView.animate` / SwiftUI `withAnimation`; respect `UIAccessibility.isReduceMotionEnabled`.
- Haptics: light/medium/heavy impact, success/warning/error notifications; don't buzz on failure.

## 3. Android conventions

- Material 3: TopAppBar, NavigationBar, FAB, Chip, BottomSheet.
- Touch targets 48dp; icons 24dp; text in `sp` with system font scaling.
- Back button/gesture must work; don't intercept predictive back.
- Theme with Material You dynamic color or brand color + semantic roles (surface/on-surface/primary).
- Motion: `animate*AsState`, `AnimatedVisibility`, shared elements; respect system animation scale (instant at 0).
- Haptics: `HapticFeedbackType.LongPress/Confirm`; don't buzz on every tap.

## 4. Web conventions

- Semantic HTML first: `button` / `nav` / `main` / `dialog` / `label`; don't build everything from divs.
- Focus is visible; the keyboard completes the main flow; Esc closes overlays; focus traps only inside modals.
- Breakpoints: `< 640` phone, `640–1024` tablet, `> 1024` desktop; content `max-width: 1280`, body text `65ch`.
- Mobile: `100dvh` instead of `100vh`; `env(safe-area-inset-*)`; inputs `font-size ≥ 16px` to prevent iOS zoom.
- Images: `srcset` + `loading="lazy"` + fixed aspect ratio; fonts `font-display: swap`.
- Motion: CSS transition/animation first; WAAPI for complex sequences; scroll effects with IntersectionObserver or scroll-timeline.
- Respect `prefers-reduced-motion` / `prefers-color-scheme` / `prefers-contrast`.

## 5. Responsive: reflow, don't crush

- Mobile first (390 wide), then extend to tablet/desktop; never build desktop and shrink it.
- Navigation: desktop sidebar → tablet collapses to an icon rail → phone bottom tabs or a drawer (pick one).
- Tables: desktop table → tablet hides secondary columns → phone card list (not horizontal scroll).
- Toolbars: flat on desktop → collapse into a "Filter" button on phone, which opens its own layer.
- Charts: side by side on desktop → single column on phone; legends become collapsible.
- A fixed bottom bar must reserve content padding so it never covers the last row.

## 6. Dark mode

- Use semantic tokens, never hard-coded colors in components; `html[data-theme="dark"]` or `prefers-color-scheme`.
- Background near-black (`#0f1115`–`#161a22`), surfaces 4–8% lighter than the background; text `#e8ecf4`, never pure white.
- Shadows read weakly in dark mode; use borders/brightness for layering instead.
- Provide dark variants or reduced brightness for images/illustrations; raise brand-color lightness for contrast.
- Remember theme choice; follow the system on first run; see `motion.md` §2.5 for the transition.

## 7. Performance

- Inline critical CSS for the first screen; defer non-critical JS; lazy-load images; subset fonts.
- Virtualize lists (> 100 items); never bind heavy handlers per item in long lists.
- Animate only `transform`/`opacity`; scroll callbacks don't read layout; `passive: true`.
- Keep main-thread tasks short (< 50ms); move heavy computation to a Worker.
- Degrade on low-end devices/low power mode: disable parallax, particles, blur, complex gradients.

## 8. Platform acceptance

- [ ] Touch targets meet platform minimums
- [ ] Navigation model follows platform conventions and back works
- [ ] Safe areas/keyboard/bottom bars never cover content
- [ ] Nothing truncates at 200% system text size
- [ ] No hard-coded colors in dark mode, no pure black or white
- [ ] Fully functional under reduced motion
- [ ] Offline/weak network shows messages and retry
- [ ] Main flow walked through on a real device (not just a simulator)
