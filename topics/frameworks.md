# Runnable recipes: CSS, Tailwind, Framer Motion, GSAP, Lottie, SwiftUI, Compose, Tauri/Electron

> The main file sets principles; this file shows what they look like in the stacks agents actually emit.
> Each recipe is minimal and complete enough to paste. All of them respect reduced motion.

## 1. Pure CSS: card enter, button press, list stagger

```css
:root {
  --ease-out: cubic-bezier(0,0,.2,1);
  --ease-in: cubic-bezier(.4,0,1,1);
  --ease-in-out: cubic-bezier(.4,0,.2,1);
  --dur-base: 200ms;
}
.card { animation: card-in 260ms var(--ease-out) both; }
@keyframes card-in { from { opacity: 0; transform: translateY(12px); } to { opacity: 1; transform: none; } }

.btn { transition: transform 80ms var(--ease-out), background-color 120ms var(--ease-out); }
.btn:active { transform: scale(.97); }

.list > li { animation: item-in 320ms var(--ease-out) both; animation-delay: calc(var(--i, 0) * 32ms); }
@keyframes item-in { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: none; } }

@media (prefers-reduced-motion: reduce) {
  .card, .list > li { animation: none; }
  .btn { transition: background-color 80ms linear; }
}
```

Scroll-driven (no JS, progressive):
```css
@supports (animation-timeline: view()) {
  .reveal { animation: card-in linear both; animation-timeline: view(); animation-range: entry 0% entry 60%; }
}
```

## 2. Tailwind v4: tokens as theme variables

```css
/* app.css */
@import "tailwindcss";
@theme {
  --radius-card: 16px;
  --ease-enter: cubic-bezier(0,0,.2,1);
  --ease-exit: cubic-bezier(.4,0,1,1);
  --animate-enter: enter 260ms var(--ease-enter) both;
  @keyframes enter { from { opacity: 0; transform: translateY(12px); } to { opacity: 1; transform: none; } }
}
```

```html
<div class="animate-enter rounded-[var(--radius-card)] bg-white p-6 shadow-sm">…</div>
<button class="h-11 rounded-lg bg-[--accent] text-[--on-accent] transition-transform duration-100 active:scale-[.97]">Save</button>
```

Squircle with fallback:
```css
@utility squircle { border-radius: 24px; corner-shape: squircle; }
```

## 3. Framer Motion (React): list enter + swipe dismiss + layout

```tsx
import { motion, AnimatePresence, useReducedMotion } from "framer-motion";

const list = { hidden: {}, show: { transition: { staggerChildren: 0.032 } } };
const item = { hidden: { opacity: 0, y: 8 }, show: { opacity: 1, y: 0, transition: { duration: 0.26, ease: [0, 0, 0.2, 1] } } };

export function List({ rows }: { rows: string[] }) {
  const reduce = useReducedMotion();
  return (
    <motion.ul variants={reduce ? undefined : list} initial="hidden" animate="show">
      <AnimatePresence>
        {rows.map(r => (
          <motion.li key={r} variants={reduce ? undefined : item}
            drag="x" dragConstraints={{ left: -320, right: 0 }} dragElastic={0.12}
            onDragEnd={(_, info) => {
              const dismiss = info.offset.x < -120 || info.velocity.x < -600;
              if (dismiss) remove(r);          // framer already inherits info.velocity
            }}>
            {r}
          </motion.li>
        ))}
      </AnimatePresence>
    </motion.ul>
  );
}
```

Spring snap-back that keeps velocity:
```tsx
animate(x, 0, { type: "spring", stiffness: 120, damping: 22, velocity: info.velocity.x });
```

## 4. GSAP: timeline for a hero, scroll reveal

```js
import gsap from "gsap";
import ScrollTrigger from "gsap/ScrollTrigger";
gsap.registerPlugin(ScrollTrigger);

const reduce = matchMedia("(prefers-reduced-motion: reduce)").matches;

if (!reduce) {
  gsap.timeline({ defaults: { ease: "power3.out", duration: 0.6 } })
    .from(".hero-title", { y: 24, opacity: 0 })
    .from(".hero-sub", { y: 16, opacity: 0 }, "-=0.35")
    .from(".hero-cta", { y: 8, opacity: 0 }, "-=0.25");

  gsap.utils.toArray(".reveal").forEach(el => {
    gsap.from(el, { y: 20, opacity: 0, scrollTrigger: { trigger: el, start: "top 85%", once: true } });
  });
}
```

Cleanup matters: `ScrollTrigger.getAll().forEach(t => t.kill())` in frameworks on unmount, or animations leak across routes.

## 5. Lottie: only for illustration, not for UI chrome

```html
<script src="https://unpkg.com/lottie-web@5/build/player/lottie_light.min.js"></script>
<script>
  const el = document.getElementById("empty-illustration");
  const anim = lottie.loadAnimation({ container: el, renderer: "svg", loop: false, autoplay: false,
    path: "/lottie/empty.json" });
  const reduce = matchMedia("(prefers-reduced-motion: reduce)").matches;
  new IntersectionObserver(([e]) => { if (e.isIntersecting) (reduce ? anim.goToAndStop(anim.totalFrames - 1, true) : anim.play()); })
    .observe(el);
</script>
```

Rules: <200KB per file, one Lottie per screen maximum, no Lottie for buttons/loaders you can do in CSS, always a static poster frame.

## 6. SwiftUI

```swift
struct CardList: View {
    @Environment(\.accessibilityReduceMotion) private var reduceMotion
    let items: [Item]

    var body: some View {
        List(items) { item in
            Row(item: item)
                .listRowSeparator(.hidden)
                .listRowBackground(Color.clear)
        }
        .listStyle(.plain)
        .scrollBounceBehavior(.basedOnSize)          // native rubber band
        .sensoryFeedback(.success, trigger: didSave)
    }
}

// Squircle card
RoundedRectangle(cornerRadius: 24, style: .continuous)
    .fill(.background)
    .overlay { content }

// Swipe dismiss with velocity (simplified)
.gesture(DragGesture().onEnded { value in
    let shouldDismiss = value.translation.width < -120 || value.predictedEndTranslation.width < -240
    withAnimation(reduceMotion ? nil : .interpolatingSpring(stiffness: 100, damping: 20)) {
        offset = shouldDismiss ? -400 : 0
    }
})
```

`predictedEndTranslation` carries the flick speed—use it instead of raw translation for the threshold.

## 7. Jetpack Compose

```kotlin
@Composable
fun SaveButton(onClick: () -> Unit) {
    var pressed by remember { mutableStateOf(false) }
    val scale by animateFloatAsState(
        targetValue = if (pressed) 0.97f else 1f,
        animationSpec = tween(80, easing = LinearOutSlowInEasing),
        label = "press"
    )
    Button(
        onClick = onClick,
        modifier = Modifier
            .graphicsLayer { scaleX = scale; scaleY = scale }
            .pointerInput(Unit) {
                detectTapGestures(onPress = { pressed = true; tryAwaitRelease(); pressed = false })
            }
    ) { Text("Save") }
}

// Swipe to dismiss
val state = rememberSwipeToDismissBoxState(
    positionalThreshold = { d -> d * 0.4f },
    confirmValueChange = { it != SwipeToDismissBoxValue.Settled }
)
SwipeToDismissBox(state = state, backgroundContent = { /* red danger surface */ }) { Card() }
```

Respect system animation scale: when `LocalMotionDurationScale.current.scaleFactor == 0f`, use `snap()`.

## 8. Tauri / Electron (desktop shells)

```ts
// Webview side: standard Web Animations, but disable OS-level bouncy scrolling you don't control
if (reduceMotion()) {
  document.documentElement.style.scrollBehavior = "auto";
}
```

```rust
// Tauri: don't block the webview's compositor; keep window effects separate
tauri::Builder::default()
  .setup(|app| { /* macOS: set window background material if asked */ Ok(()) })
  .run(tauri::generate_context!())?;
```

Electron: `webPreferences: { enableBlinkFeatures: "CSSScrollbars" }` is not needed for motion; what matters is not disabling hardware acceleration, and keeping the frame rate at 60 by avoiding heavy `backdrop-filter` across large areas.

## 9. Framework selection guide

| Need | Use | Avoid |
| --- | --- | --- |
| One-off hover/press/enter | CSS | a JS animation library |
| List enter/exit/order changes | Framer Motion `layout` / `AnimatePresence` | manual FLIP |
| Complex timeline or scroll choreography | GSAP + ScrollTrigger | hand-rolled rAF chains |
| Illustration / onboarding | Lottie, ≤1 per screen | Lottie as a loader |
| Gesture physics | platform spring + velocity | CSS keyframes |
| Desktop chrome effects | native window APIs | simulating macOS materials in CSS |

## 10. Verification per stack

- Log actual frame times with `performance.mark` / `PerformanceObserver('longtask')`.
- Test with CPU throttling 4× on mobile; if it drops below 45fps, simplify before shipping.
- Check reduced motion on a real device; DevTools emulation does not cover the OS-level setting on iOS/Android.
- For springs, log release velocity and confirm it is used (non-zero into the animation).
