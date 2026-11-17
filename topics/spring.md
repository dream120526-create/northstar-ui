# Spring motion branch: tactile, velocity-continuous scrolling, drag, and swipe-to-dismiss

> **Scope**: this is an *optional branch* of the motion system, not a replacement. Use it for list scrolling, card swipes, pull-to-refresh, swipe-to-dismiss, and rubber-band overscroll.
> **Targets**: Web, iOS, Android, Windows, and cross-platform shells (IPA / APK / EXE / Tauri / Flutter / React Native).

## 0. How this fits the main skill

- Classification → structure → hierarchy → motion still applies. A jelly-like bounce cannot rescue an unclassified page.
- This branch adds **two** things: spring dynamics for gesture-driven surfaces, and squircle (continuous-corner) radii.
- It does not change the four-curve rule for ordinary UI: enter = `ease-out`, exit = `ease-in`, in-page movement = `ease-in-out`, constant speed = `linear`. Springs are for **gestures and interruption**, not for a fade-in.

## 1. Hard constraints

1. **Dynamics first.**
   - Model with spring dynamics (`mass`, `stiffness`, `dampingRatio`, initial velocity). Do not fake a spring with a fixed-duration bezier or `ease`.
   - Release must inherit the gesture's instantaneous velocity. Animation must support **interruptible, preemptive** transitions: the new target takes over from the *current position and velocity*.
   - Edge overscroll uses a low-loss elastic rebound (rubber band). Never hard-stop at the edge.
2. **Baseline parameters (shared vocabulary)**

   | Parameter | Range | Default | Meaning |
   | --- | --- | --- | --- |
   | `mass` | 0.6–1.4 | 1.0 | Higher = heavier, slower |
   | `stiffness` | 60–160 | 100 | Higher = snappier pull |
   | `dampingRatio` | 0.7–0.95 | 0.8 | 1 = no oscillation; < 0.7 feels shaky |
   | `bounceFactor` | 0.15–0.4 | 0.25 | Edge rebound strength |

3. **Platform mapping and fallback order (must follow)**
   - **Native spring API exists** → use it, map the semantic parameters, keep dynamic behavior aligned:
     - iOS: `CASpringAnimation` / SwiftUI `.spring(response:dampingFraction:blendDuration:)` / `UIScrollView` for scroll;
     - Android: Compose `Animatable` + `spring()`, `OverscrollEffect`;
     - Windows: `SpringNaturalMotionAnimation` (Composition API);
     - Web: Web Animations `spring()` where supported, `framer-motion` / `react-spring`, or the solver below.
   - **No native spring** → use a numeric integrator (semi-implicit Euler or RK4) that carries velocity.
   - **Minimal/static CSS only** → mark it as a **degraded mode**, state that velocity inheritance is lost, and never claim it feels native.
4. **Forbidden**
   - ❌ Fixed-millisecond "spring" rebound.
   - ❌ Dropping the release velocity.
   - ❌ Hard stop at the boundary.
   - ❌ Parameter combinations that jitter, stall, or feel plastic.

## 2. Scenario tags (for matching)

- `list bounce` — list overscroll at top/bottom
- `swipe dismiss` — swipe a card or sheet away
- `rubber band` — dragging past a bound
- `spring snap` — drag with a snap point (carousel, segmented control)

## 3. Web solver (works everywhere, carries velocity)

```js
// One tiny spring integrator shared by every recipe below.
export class Spring {
  constructor({ stiffness = 100, dampingRatio = 0.8, mass = 1, value = 0, velocity = 0, onUpdate }) {
    this.k = stiffness; this.c = 2 * dampingRatio * Math.sqrt(stiffness * mass); this.m = mass;
    this.x = value; this.v = velocity; this.target = value; this.onUpdate = onUpdate;
    this.raf = 0; this.last = 0;
  }
  setTarget(target, velocity = this.v) { this.target = target; this.v = velocity; this.start(); }
  start() {
    if (this.raf) return;
    this.last = performance.now();
    const step = now => {
      const dt = Math.min((now - this.last) / 1000, 1 / 30); // clamp tab-switch spikes
      this.last = now;
      // semi-implicit Euler: stable enough at 60–120fps
      const a = (-this.k * (this.x - this.target) - this.c * this.v) / this.m;
      this.v += a * dt;
      this.x += this.v * dt;
      this.onUpdate(this.x, this.v);
      if (Math.abs(this.v) < 0.01 && Math.abs(this.x - this.target) < 0.1) {
        this.x = this.target; this.v = 0; this.raf = 0; this.onUpdate(this.x, 0); return;
      }
      this.raf = requestAnimationFrame(step);
    };
    this.raf = requestAnimationFrame(step);
  }
  stop() { cancelAnimationFrame(this.raf); this.raf = 0; }
}
```

## 4. Recipe A — swipe to dismiss (velocity inherit + preemptive)

```js
export function enableSwipeDismiss(el, { onDismiss } = {}) {
  const spring = new Spring({ value: 0, dampingRatio: 0.85, stiffness: 120,
    onUpdate: x => { el.style.transform = `translate3d(${x}px,0,0)`; } });

  let startX = 0, startT = 0, dragging = false, lastX = 0, lastT = 0, velocity = 0;

  el.addEventListener('pointerdown', e => {
    if (reduceMotion()) return;
    spring.stop();                      // preempt: keep current x and v
    dragging = true; startX = lastX = e.clientX; startT = lastT = performance.now();
    el.setPointerCapture(e.pointerId); el.style.transition = 'none';
  });
  el.addEventListener('pointermove', e => {
    if (!dragging) return;
    const now = performance.now();
    velocity = (e.clientX - lastX) / Math.max(now - lastT, 1) * 1000; // px/s
    lastX = e.clientX; lastT = now;
    const dx = e.clientX - startX;
    el.style.transform = `translate3d(${dx < 0 ? dx * 0.6 : dx}px,0,0)`; // rubber band right
  });
  const end = () => {
    if (!dragging) return;
    dragging = false;
    const dx = lastX - startX;
    const shouldDismiss = dx < -120 || velocity < -600;   // distance OR flick
    if (shouldDismiss) {
      spring.x = dx; spring.v = velocity;                  // inherit release velocity
      let fired = false;
      spring.onUpdate = x => {
        el.style.transform = `translate3d(${x}px,0,0)`;
        if (!fired && Math.abs(x) >= el.offsetWidth * 0.9) { fired = true; onDismiss?.(); }
      };
      spring.setTarget(-el.offsetWidth * 1.2, velocity);
    } else {
      spring.x = dx; spring.setTarget(0, velocity);        // bounce back with speed
    }
  };
  el.addEventListener('pointerup', end);
  el.addEventListener('pointercancel', end);
}
```

Why it feels native: the card never freezes on release, and grabbing it mid-flight continues from the current velocity instead of restarting.

## 5. Recipe B — rubber-band overscroll for a custom scroll container

```js
export function enableRubberBand(scroller, { bounceFactor = 0.25 } = {}) {
  const spring = new Spring({ value: 0, stiffness: 100, dampingRatio: 0.8,
    onUpdate: y => { scroller.style.transform = `translate3d(0,${y}px,0)`; } });
  scroller.addEventListener('wheel', e => {
    const atTop = scroller.scrollTop <= 0, atBottom =
      scroller.scrollTop + scroller.clientHeight >= scroller.scrollHeight - 1;
    if ((atTop && e.deltaY < 0) || (atBottom && e.deltaY > 0)) {
      const over = spring.x + e.deltaY * bounceFactor;      // damped overscroll
      spring.stop(); spring.x = over; spring.v = e.deltaY * 1000 * bounceFactor;
      spring.onUpdate(over, spring.v);
      clearTimeout(spring._t);
      spring._t = setTimeout(() => spring.setTarget(0), 90); // release → elastic return
    }
  }, { passive: true });
}
```

## 6. Recipe C — spring snap (carousel / segmented control)

```js
export function springSnap(el, points, { stiffness = 120, dampingRatio = 0.85 } = {}) {
  const spring = new Spring({ value: points[0], stiffness, dampingRatio,
    onUpdate: x => { el.style.transform = `translate3d(${x}px,0,0)`; } });
  el.addEventListener('pointerup', e => {
    const nearest = points.reduce((a, b) => Math.abs(b - spring.x) < Math.abs(a - spring.x) ? b : a);
    spring.setTarget(nearest, spring.v);     // keep flick speed toward the snap point
  });
  return spring;
}
```

## 7. Platform parameter mapping

| Semantic | iOS (`UISpringTimingParameters`) | Compose `spring()` | Windows | Web (`framer-motion`) |
| --- | --- | --- | --- | --- |
| stiffness 100 | natural frequency ≈ `2π√(k/m)` | `Spring.StiffnessMedium` (≈1500, see note) | `spring` scalar | `stiffness: 100` |
| dampingRatio 0.8 | `damping: 0.8` | `Spring.DampingRatioNoBouncy` = 1, use 0.8 literal | `damping` ratio | `damping: 20` |
| mass 1.0 | implicit | `Spring.DampingRatioMediumBouncy` fallback | `mass` | `mass: 1` |
| release velocity | `initialVelocity` (normalized) | `Animatable.animateTo(initialVelocity = v)` | `velocity` | `velocity: v` |

**Note**: Compose and framer-motion use different units for stiffness/damping (mass-spring vs. response-based). Map **behavior**, not numbers: tune so a 3 cm flick settles in ~380–450ms with one visible overshoot of ≤6%. Always pass release velocity through; dropping it is the most common reason a spring feels wrong.

| Platform | Scroll bounce | Swipe dismiss | Snap |
| --- | --- | --- | --- |
| iOS | `UIScrollView` `.vertical` bounce (native) | `UIScrollView` / custom `UIPanGestureRecognizer` + `CASpringAnimation` | `UICollectionView` paging |
| Android | `OverscrollEffect` (`Modifier.overscroll`) | `AnchoredDraggable` + `spring()` | `AnchoredDraggable` |
| Windows | `ScrollViewer` inertia | `SpringNaturalMotionAnimation` | `ScrollViewer` snap points |
| Web | native scroll + `overscroll-behavior` or Recipe B | Recipe A | Recipe C |

## 8. Squircle: continuous corner radius

Standard CSS `border-radius` draws a *circular* arc joined to straight edges; the curvature jumps at the join, which is why Apple-shaped cards look softer. A **squircle** (superellipse) keeps curvature continuous.

```css
/* Progressive enhancement: supported browsers get a true squircle */
.squircle {
  border-radius: 24px;
  corner-shape: squircle;        /* CSS Borders Level 4, progressive */
}
@supports not (corner-shape: squircle) {
  .squircle { border-radius: 26px; }  /* +2px optical compensation */
}
```

When `corner-shape` is unavailable, render the squircle with a clip path generated from a superellipse (iOS: `UIBezierPath` with continuous corners; Android: `RoundedCornerShape` + custom `Path`; Windows: `CompositionRoundedRectangleGeometry`). See `topics/recipes.md` for the generator and the iOS/Android equivalents.

Rules:
- Pick **one** radius family per product; do not mix circular and squircle corners on the same surface.
- Optical compensation: squircle looks slightly smaller than a circle of the same value, so add ~1–2px or use `corner-shape` natively.
- Nested surfaces: inner radius = outer radius − padding, in the same family.

## 9. Tuning guide

| Symptom | Change |
| --- | --- |
| Feels sluggish / laggy | Raise `stiffness` (100 → 140), keep damping |
| Too bouncy / jittery | Raise `dampingRatio` (0.8 → 0.92) |
| Feels plastic, stops dead | Lower damping slightly (0.8 → 0.75) **or** check you are passing release velocity |
| Overshoot too large | Raise stiffness + damping together; never fix it by lowering velocity |
| Edge feels stiff | Raise `bounceFactor` (0.25 → 0.35) |
| Long lists feel heavy | Virtualize, and use native scroll instead of a custom solver |

## 10. Acceptance for this branch

- [ ] Release inherits real gesture velocity (log `v` at pointerup; it must be non-zero for flicks)
- [ ] Interrupting mid-flight continues from current position and velocity, no jump
- [ ] Edge overscroll returns elastically, never hard-stops
- [ ] No fixed-duration fake spring in gesture paths
- [ ] Reduced motion: gestures snap instantly, no bounce
- [ ] 60fps (120 on ProMotion) with 20+ draggable rows; no layout thrash per frame
- [ ] If a degraded mode is used, the UI text or docs say so explicitly

## 11. Native snippets (copy-ready)

### iOS — CASpringAnimation with gesture velocity

```swift
// Triggered from a UIPanGestureRecognizer's .ended state.
func springTo(_ x: CGFloat, velocity: CGFloat) {
    let anim = CASpringAnimation(keyPath: "position.x")
    anim.mass = 1.0
    anim.stiffness = 100
    anim.damping = 20          // dampingRatio 0.8 × 2√(k·m) = 0.8 × 20
    anim.initialVelocity = velocity
    anim.fromValue = card.layer.position.x
    anim.toValue = x
    anim.duration = anim.settlingDuration
    card.layer.add(anim, forKey: "springX")
    card.layer.position.x = x
}
```

```swift
// Squircle corner (continuous curvature)
let path = UIBezierPath(roundedRect: bounds, cornerRadius: 24, style: .continuous)
```

### Compose — AnchoredDraggable swipe dismiss

```kotlin
val density = LocalDensity.current
val state = remember { AnchoredDraggableState(
    initialValue = DragValue.Rest,
    positionalThreshold = { distance: Float -> distance * 0.4f },
    velocityThreshold = { with(density) { 600.dp.toPx() } },
    animationSpec = spring(dampingRatio = 0.85f, stiffness = 120f)
) }
// Modifier.anchoredDraggable(state, Orientation.Horizontal) + Modifier.overscroll(...)
```

Squircle: `RoundedCornerShape(24.dp)` uses continuous-ish corners on Android 12+; for exact superellipse, generate a `Path`.

### Windows — Composition spring

```csharp
var spring = compositor.CreateSpringScalarAnimation();
spring.Target = "Offset.X";
spring.Stiffness = 100f;         // map from product spacing scale
spring.DampingRatio = 0.8f;
spring.Period = TimeSpan.FromMilliseconds(50);
spring.InitialVelocity = gestureVelocity;
```

### Web — framer-motion

```js
import { motion, useMotionValue, animate } from 'framer-motion';
const x = useMotionValue(0);
// on dragEnd(event, info): info.velocity is px/s
animate(x, shouldDismiss ? -320 : 0, {
  type: 'spring', stiffness: 120, damping: 22, mass: 1,
  velocity: info.velocity.x,           // <- never drop this
});
```

## 12. Liquid Glass (optional OS material)

On iOS 26+ and macOS Tahoe, Apple exposes Liquid Glass. It is **opt-in per product**, because it changes how much content is visible under bars and in overlays.

**Ask before adding it.** One line is enough:

> "Does this product target iOS 26+ / macOS Tahoe, and do you want Liquid Glass on navigation bars, toolbars, and floating controls? (Yes / match system / skip)"

Rules:
- Use it only on the **functional floating layer** (nav bar, toolbar, sheet header, floating button). Never behind body text, tables, or forms.
- Always provide a fallback: `#available(iOS 26, *)` → `glassEffect()`, else a solid or blurred surface with the same contrast.
- Re-check contrast after the material is applied; glass over busy imagery can drop below 4.5:1. If it does, add a scrim or skip glass on that screen.
- Respect "Reduce Transparency" and "Increase Contrast"; when on, replace glass with an opaque surface.
- Do not simulate glass on Android or Web by default; if the user asks for a similar look, use a *brand material* token, not an imitation of a platform API.

```swift
if #available(iOS 26, *) {
    content.glassEffect(.regular, in: .rect(cornerRadius: 22))
} else {
    content.background(.regularMaterial, in: RoundedRectangle(cornerRadius: 22, style: .continuous))
}
```
