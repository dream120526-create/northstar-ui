# Motion recipes: curves, duration, button feedback, and 16 UI effects

> This topic is the authoritative motion doc. The main file covers principles and the index only; here are copy-ready implementations.
> All examples default to Web; §6 gives SwiftUI / Compose / UIKit equivalents.
> Read §1 tokens and curves first, then pick recipes by number. Every recipe includes a reduced-motion fallback.

---

## 1. Tokens: unify first, then animate

```css
:root {
  /* Duration */
  --dur-press: 80ms;    /* Press feedback */
  --dur-fast: 120ms;    /* Icon swap, hover */
  --dur-base: 200ms;    /* Buttons, tooltips, standard micro-interactions */
  --dur-slow: 280ms;    /* Overlays, card expand */
  --dur-page: 320ms;    /* Page/full-screen transition */
  --dur-exit: 160ms;    /* Exit: 70–80% of enter */

  /* Easing */
  --ease-linear: linear;                          /* Constant speed */
  --ease-in: cubic-bezier(.4, 0, 1, 1);           /* Exit: accelerate out */
  --ease-out: cubic-bezier(0, 0, .2, 1);          /* Enter: settle in */
  --ease-in-out: cubic-bezier(.4, 0, .2, 1);      /* In-page A→B */
  --ease-spring: cubic-bezier(.34, 1.56, .64, 1); /* Feedback bounce only */
}

/* Global fallback: keep state and opacity; remove movement, scale, rotation, parallax */
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: .01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: .01ms !important;
    scroll-behavior: auto !important;
  }
  .reveal, .parallax, .tilt, .magnetic { transform: none !important; opacity: 1 !important; }
}
```

JS shared entry points:

```js
export const reduceMotion = () =>
  matchMedia('(prefers-reduced-motion: reduce)').matches;

export const canHover = () => matchMedia('(hover: hover) and (pointer: fine)').matches;

export const haptic = (ms = 10) => {
  if (reduceMotion()) return;
  navigator.vibrate?.(ms); // Android/some browsers; see §6 for iOS
};
```

**Curve choice in four lines**: constant speed uses `linear`; exit uses `--ease-in`; enter uses `--ease-out`; in-page movement uses `--ease-in-out`. Bounce only for likes, badges, and drag snap-back.

---

## 2. Six button-level micro-interactions

### 2.1 Hold to confirm (border-fill progress + bounce + done)

```html
<button class="hold" type="button" aria-pressed="false">
  <svg class="hold__ring" viewBox="0 0 200 52" aria-hidden="true">
    <rect x="1" y="1" width="198" height="50" rx="12"
          pathLength="1" fill="none" stroke="currentColor" stroke-width="2"/>
  </svg>
  <span class="hold__label">Hold 0.7s to confirm</span>
</button>
```

```css
.hold {
  position: relative; display: inline-flex; align-items: center; justify-content: center;
  min-width: 200px; height: 52px; padding: 0 20px; border: 0; border-radius: 12px;
  background: var(--surface-2, #1f2430); color: var(--text, #e8ecf4);
  font: 600 16px/1 system-ui, sans-serif; cursor: pointer; touch-action: none;
  -webkit-user-select: none; user-select: none; overflow: hidden;
}
.hold::before { /* Subtle inner fill as hold feedback */
  content: ""; position: absolute; inset: 0; background: currentColor;
  opacity: 0; transition: opacity var(--dur-fast) var(--ease-out);
}
.hold.is-holding::before { opacity: .06; }
.hold__ring { position: absolute; inset: 0; width: 100%; height: 100%; pointer-events: none; }
.hold__ring rect {
  stroke-dasharray: 1; stroke-dashoffset: 1;
  stroke: var(--accent, #2f5fe0);
}
.hold__label { position: relative; z-index: 1; }
.hold.is-done { background: var(--success, #157a4d); color: var(--on-accent, #fff); }
.hold.is-done .hold__ring { opacity: 0; }
```

```js
const HOLD_MS = 700;

export function initHold(btn, onConfirm) {
  const rect = btn.querySelector('rect');
  const label = btn.querySelector('.hold__label');
  let raf = 0, start = 0, done = false, pid = null;

  const setProgress = p => { rect.style.strokeDashoffset = String(1 - p); };

  const reset = () => {
    cancelAnimationFrame(raf);
    btn.classList.remove('is-holding');
    // Clear with bounce: short, ease-out, no drag
    rect.style.transition = `stroke-dashoffset 150ms cubic-bezier(0,0,.2,1)`;
    setProgress(0);
    setTimeout(() => { rect.style.transition = ''; }, 170);
  };

  const tick = now => {
    const p = Math.min((now - start) / HOLD_MS, 1);
    setProgress(p);
    if (p >= 1) { finish(); return; }
    raf = requestAnimationFrame(tick);
  };

  const startHold = () => {
    if (done) return;
    rect.style.transition = '';       // Clear bounce transition so it doesn't slow rAF
    start = performance.now();
    btn.classList.add('is-holding');
    raf = requestAnimationFrame(tick);
  };

  const finish = () => {
    cancelAnimationFrame(raf);
    done = true;
    setProgress(1);
    btn.classList.remove('is-holding');
    btn.classList.add('is-done');
    btn.setAttribute('aria-pressed', 'true');
    label.textContent = 'Confirmed';
    haptic(12);
    onConfirm?.();
  };

  const endHold = () => {
    // Release pointer capture to avoid stale capture after pointercancel / blur
    if (pid !== null && btn.hasPointerCapture?.(pid)) {
      try { btn.releasePointerCapture(pid); } catch {}
    }
    pid = null;
    if (!done) reset();
  };

  btn.addEventListener('pointerdown', e => {
    pid = e.pointerId;
    btn.setPointerCapture(pid);
    startHold();
  });
  btn.addEventListener('pointerup', endHold);
  btn.addEventListener('pointercancel', endHold);
  btn.addEventListener('keydown', e => {
    if ((e.key === ' ' || e.key === 'Enter') && !e.repeat) { e.preventDefault(); startHold(); }
  });
  btn.addEventListener('keyup', e => {
    if ((e.key === ' ' || e.key === 'Enter') && !done) reset();
  });
  // Always clean up on page blur so hold state can't get stuck
  addEventListener('blur', endHold);
}
```

Key points:
- Progress uses `pathLength=1` + `stroke-dashoffset`; any size, no circumference recalculation. Progress itself is **constant speed**, so advance linearly via rAF.
- Completion is a state: update label, `aria-pressed`, color, and haptics together — don't just draw a ring.
- 700ms is a safe threshold, not decoration time; under reduce-motion **keep the wait**, drop only the inner fill pulse.

---

### 2.2 Like burst (spring icon + particles + rolling count)

```html
<button class="like" type="button" aria-pressed="false" aria-label="Like, currently 128">
  <span class="like__icon" aria-hidden="true">
    <svg viewBox="0 0 24 24"><path d="M12 21s-7.5-4.7-9.3-9A5.4 5.4 0 0 1 12 6.6 5.4 5.4 0 0 1 21.3 12c-1.8 4.3-9.3 9-9.3 9Z"/></svg>
  </span>
  <span class="like__count" aria-hidden="true"><span>128</span></span>
  <span class="like__particles" aria-hidden="true"></span>
</button>
```

```css
.like { position: relative; display: inline-flex; align-items: center; gap: 8px;
  border: 0; background: transparent; color: var(--text-2, #9aa6ba);
  font: 600 16px/1 system-ui, sans-serif; cursor: pointer; padding: 10px; }
.like__icon svg { width: 24px; height: 24px; fill: none; stroke: currentColor; stroke-width: 1.8; }
.like.is-liked { color: var(--like, #c93030); }
.like.is-liked .like__icon svg { fill: currentColor; }
.like__icon { display: inline-flex; will-change: transform; }
.like.is-burst .like__icon { animation: like-pop 320ms var(--ease-spring); }
@keyframes like-pop {
  0% { transform: scale(1); }
  40% { transform: scale(1.25); }
  100% { transform: scale(1); }
}
.like__particles { position: absolute; left: 22px; top: 50%; width: 0; height: 0; pointer-events: none; }
.like__particles i {
  position: absolute; width: 5px; height: 5px; border-radius: 50%;
  background: currentColor; opacity: 0;
}
.like__count { overflow: hidden; height: 1em; display: inline-block; position: relative; min-width: 3ch; }
.like__count > span { display: block; }
```

```js
export function initLike(btn, initial, onChange) {
  const icon = btn.querySelector('.like__icon');
  const layer = btn.querySelector('.like__particles');
  const countBox = btn.querySelector('.like__count');
  let count = initial, liked = false;

  const rollCount = next => {
    if (reduceMotion()) { countBox.firstElementChild.textContent = next; return; }
    const old = countBox.firstElementChild;
    const fresh = document.createElement('span');
    fresh.textContent = next;
    fresh.style.cssText = 'position:absolute;inset:0;transform:translateY(100%);opacity:0';
    countBox.append(fresh);
    old.animate(
      [{ transform: 'translateY(0)', opacity: 1 }, { transform: 'translateY(-100%)', opacity: 0 }],
      { duration: 240, easing: 'cubic-bezier(0,0,.2,1)', fill: 'forwards' }
    ).onfinish = () => old.remove();
    const anim = fresh.animate(
      [{ transform: 'translateY(100%)', opacity: 0 }, { transform: 'translateY(0)', opacity: 1 }],
      { duration: 240, easing: 'cubic-bezier(0,0,.2,1)', fill: 'forwards' }
    );
    anim.onfinish = () => {
      anim.cancel();                  // Remove fill first, or it overrides inline styles
      fresh.style.position = 'static';
      fresh.style.transform = 'none';
      fresh.style.opacity = '1';
    };
  };

  const burst = () => {
    if (reduceMotion()) return;
    const n = 10;
    for (let i = 0; i < n; i++) {
      const p = document.createElement('i');
      const angle = (Math.PI * 2 * i) / n + (Math.random() - .5) * .5;
      const dist = 20 + Math.random() * 16;
      const x = Math.cos(angle) * dist;
      const y = Math.sin(angle) * dist;
      layer.append(p);
      p.animate(
        [
          { transform: 'translate(-50%,-50%) scale(1)', opacity: 1 },
          { transform: `translate(calc(-50% + ${x}px), calc(-50% + ${y}px)) scale(.2)`, opacity: 0 }
        ],
        { duration: 420, easing: 'cubic-bezier(0,0,.2,1)' }
      ).onfinish = () => p.remove();
    }
  };

  btn.addEventListener('click', () => {
    liked = !liked;
    count += liked ? 1 : -1;
    btn.classList.toggle('is-liked', liked);
    btn.setAttribute('aria-pressed', String(liked));
    btn.setAttribute('aria-label', `Like, currently ${count}`);
    btn.classList.remove('is-burst');
    void btn.offsetWidth;           // Restart animation
    btn.classList.add('is-burst');
    rollCount(count);
    if (liked) { burst(); haptic(8); }
    onChange?.(liked, count);
  });
}
```

Key points:
- Particles use `transform` + `opacity`, about 10; switch to Canvas above 30.
- Don't stack particle layers on rapid taps: clear `layer` or cap concurrency each time.
- Count rolls with two spans over 240ms; container `overflow:hidden`; `tabular-nums` prevents width jitter.
- reduce-motion: color change + instant number only; no scale, no burst.

---

### 2.3 Slide to confirm (drag tracking + background fill + snap/bounce)

```html
<div class="slide" role="slider" tabindex="0" aria-label="Slide to confirm payment"
     aria-valuemin="0" aria-valuemax="100" aria-valuenow="0">
  <div class="slide__fill" aria-hidden="true"></div>
  <span class="slide__hint">Slide to confirm payment</span>
  <div class="slide__thumb" aria-hidden="true">
    <svg class="slide__arrow" viewBox="0 0 24 24"><path d="M5 12h14M13 6l6 6-6 6"/></svg>
    <svg class="slide__check" viewBox="0 0 24 24"><path d="M5 13l4 4L19 7"/></svg>
  </div>
</div>
```

```css
.slide {
  position: relative; width: 300px; max-width: 100%; height: 56px; border-radius: 999px;
  background: var(--surface-2, #1f2430); overflow: hidden; touch-action: none;
  -webkit-user-select: none; user-select: none; cursor: grab;
}
.slide__fill {
  position: absolute; inset: 0; transform-origin: left center; transform: scaleX(0);
  background: var(--accent-soft, #2c4a8a); will-change: transform;
}
.slide__hint {
  position: absolute; inset: 0; display: grid; place-items: center;
  color: var(--text-2, #9aa6ba); font: 500 16px/1 system-ui, sans-serif;
  transition: opacity var(--dur-fast) var(--ease-out);
}
.slide__hint.is-hidden { opacity: 0; }
.slide__thumb {
  position: absolute; top: 4px; left: 4px; width: 48px; height: 48px; border-radius: 50%;
  display: grid; place-items: center; background: var(--accent, #2f5fe0); color: var(--on-accent, #fff);
  will-change: transform; cursor: grab;
}
.slide__thumb svg { width: 22px; height: 22px; fill: none; stroke: currentColor;
  stroke-width: 2; stroke-linecap: round; stroke-linejoin: round; }
.slide__check { display: none; }
.slide.is-done { background: var(--success, #157a4d); }
.slide.is-done .slide__fill { background: var(--success, #157a4d); }
.slide.is-done .slide__hint { opacity: 0; }
.slide.is-done .slide__arrow { display: none; }
.slide.is-done .slide__check { display: block; }
```

```js
export function initSlide(el, onConfirm) {
  const fill = el.querySelector('.slide__fill');
  const thumb = el.querySelector('.slide__thumb');
  const hint = el.querySelector('.slide__hint');
  const max = () => el.clientWidth - thumb.offsetWidth - 8;
  let x = 0, dragging = false, done = false, startX = 0, raf = 0;

  const paint = p => {
    x = p * max();
    fill.style.transform = `scaleX(${p})`;   // transform only
    thumb.style.transform = `translateX(${x}px)`;
    el.setAttribute('aria-valuenow', String(Math.round(p * 100)));
    // Toggle class only when crossing threshold; avoid inline writes every frame
    const hide = p > .55;
    if (hint.classList.contains('is-hidden') !== hide) hint.classList.toggle('is-hidden', hide);
  };

  const settle = (to, cb) => {
    const from = x;
    const dur = to > from ? 180 : 220;
    const anim = thumb.animate(
      [{ transform: `translateX(${from}px)` }, { transform: `translateX(${to}px)` }],
      { duration: dur, easing: 'cubic-bezier(0,0,.2,1)' }
    );
    fill.animate(
      [{ transform: `scaleX(${from / max()})` }, { transform: `scaleX(${to / max()})` }],
      { duration: dur, easing: 'cubic-bezier(0,0,.2,1)' }
    );
    anim.onfinish = () => {
      x = to;
      thumb.style.transform = `translateX(${to}px)`;
      fill.style.transform = `scaleX(${to / max()})`;
      cb?.();
    };
  };

  const success = () => {
    if (done) return;
    done = true;
    el.classList.add('is-done');
    el.setAttribute('aria-valuenow', '100');
    settle(max(), () => { haptic(12); onConfirm?.(); });
  };

  const back = () => {
    if (done) return;
    settle(0, () => paint(0));
  };

  const onMove = e => {
    if (!dragging || done) return;
    const p = Math.min(Math.max((e.clientX - startX) / max(), 0), 1);
    if (!raf) raf = requestAnimationFrame(() => { paint(p); raf = 0; });
    if (p >= .92) { dragging = false; success(); }
  };

  thumb.addEventListener('pointerdown', e => {
    if (done) return;
    thumb.getAnimations().forEach(a => a.cancel());   // Re-drag before bounce finishes
    fill.getAnimations().forEach(a => a.cancel());
    dragging = true;
    startX = e.clientX - x;
    thumb.setPointerCapture(e.pointerId);
    el.style.cursor = 'grabbing';
  });
  thumb.addEventListener('pointermove', onMove);
  const end = () => {
    if (!dragging) return;
    dragging = false;
    el.style.cursor = '';
    const p = x / max();
    if (p >= .9) success(); else back();
  };
  thumb.addEventListener('pointerup', end);
  thumb.addEventListener('pointercancel', end);

  // Keyboard equivalent
  el.addEventListener('keydown', e => {
    if (done) return;
    const p = x / max();
    if (e.key === 'ArrowRight') { e.preventDefault(); paint(Math.min(p + .1, 1)); }
    if (e.key === 'ArrowLeft') { e.preventDefault(); paint(Math.max(p - .1, 0)); }
    if (e.key === 'End') { e.preventDefault(); success(); }
    if (e.key === 'Enter') { e.preventDefault(); p >= .9 ? success() : back(); }
  });
}
```

Key points:
- Fill with `scaleX`, never animate `width`; thumb tracks 1:1.
- Threshold 90–92%; snap to done immediately at threshold, don't wait for release.
- Snap-back 220ms ease-out; success 180ms ease-out + checkmark + light haptic.
- Touch target 56px tall, thumb 48px; support keyboard on desktop.

---

### 2.4 Add to cart (parabolic fly-in + badge bounce)

```html
<div class="pdp">
  <img class="pdp__img" src="product.jpg" alt="Product">
  <button class="add" type="button">Add to cart</button>
  <button class="cart" type="button" aria-label="Cart, 2 items">
    <svg viewBox="0 0 24 24"><path d="M3 4h2l2.2 10.2a2 2 0 0 0 2 1.6h7.9a2 2 0 0 0 2-1.5L21 8H6"/><circle cx="10" cy="20" r="1.4"/><circle cx="18" cy="20" r="1.4"/></svg>
    <span class="cart__badge" aria-hidden="true">2</span>
  </button>
</div>
```

```css
.cart { position: relative; width: 44px; height: 44px; border: 0; background: transparent; color: inherit; }
.cart svg { width: 24px; height: 24px; fill: none; stroke: currentColor; stroke-width: 1.8;
  stroke-linecap: round; stroke-linejoin: round; }
.cart__badge {
  position: absolute; top: 2px; right: 2px; min-width: 18px; height: 18px; padding: 0 5px;
  border-radius: 999px; background: var(--like, #c93030); color: var(--on-accent, #fff);
  font: 700 13px/18px system-ui, sans-serif; text-align: center;
}
.cart__badge.is-bump { animation: badge-bump 300ms var(--ease-spring); }
@keyframes badge-bump {
  0% { transform: scale(1); }
  35% { transform: scale(1.35); }
  70% { transform: scale(.9); }
  100% { transform: scale(1); }
}
.fly-x, .fly-y { position: fixed; left: 0; top: 0; pointer-events: none; z-index: 9999; }
.fly-x img, .fly-y img { display: block; border-radius: 10px; }
```

```js
export function flyToCart(img, cartBtn, onArrive) {
  if (reduceMotion()) { onArrive?.(); return; }

  const from = img.getBoundingClientRect();
  const to = cartBtn.getBoundingClientRect();
  const dx = to.left + to.width / 2 - (from.left + from.width / 2);
  const dy = to.top + to.height / 2 - (from.top + from.height / 2);

  // Outer layer moves X: linear; inner moves Y: ease-in (gravity). Combined = parabola.
  const wrapX = document.createElement('div');
  const wrapY = document.createElement('div');
  wrapX.className = 'fly-x';
  wrapY.className = 'fly-y';
  wrapX.style.cssText = `transform:translate(${from.left}px,${from.top}px)`;
  const ghost = img.cloneNode(true);
  ghost.style.cssText = `width:${from.width}px;height:${from.height}px;object-fit:cover`;
  wrapY.append(ghost);
  wrapX.append(wrapY);
  document.body.append(wrapX);

  const xAnim = wrapX.animate(
    [{ transform: `translate(${from.left}px,${from.top}px)` },
     { transform: `translate(${from.left + dx}px,${from.top}px)` }],
    { duration: 560, easing: 'linear', fill: 'forwards' }
  );
  const yAnim = wrapY.animate(
    [{ transform: 'translateY(0) scale(1)', opacity: 1 },
     { transform: `translateY(${dy}px) scale(.18)`, opacity: .55 }],
    { duration: 560, easing: 'cubic-bezier(.4,0,1,1)', fill: 'forwards' }
  );

  xAnim.onfinish = () => {
    wrapX.remove();
    onArrive?.();             // Add +1 to count and bump badge here
  };
}
```

```js
// Count + badge
export function bumpBadge(badge, next) {
  badge.textContent = String(next);
  if (reduceMotion()) return;
  badge.classList.remove('is-bump');
  void badge.offsetWidth;
  badge.classList.add('is-bump');
  haptic(8);
}
```

Key points:
- Clone only one image; fly layer `position:fixed` + `pointer-events:none`.
- Don't hand-tune a bezier for the parabola: **X linear + Y ease-in** is gravity; more compatible than `offset-path`.
- 500–650ms; compress to 400ms for very short distances.
- On finish, actually add to cart + update badge; animation failure must not break business logic. With reduce-motion, add directly.

---

### 2.5 Theme switch (sun rotates into moon + circular reveal)

```html
<button class="theme" type="button" aria-label="Toggle theme" aria-pressed="false">
  <svg class="theme__sun" viewBox="0 0 24 24"><circle cx="12" cy="12" r="4"/><path d="M12 2v2M12 20v2M2 12h2M20 12h2M4.9 4.9l1.4 1.4M17.7 17.7l1.4 1.4M19.1 4.9l-1.4 1.4M6.3 17.7l-1.4 1.4"/></svg>
  <svg class="theme__moon" viewBox="0 0 24 24"><path d="M21 12.8A8.5 8.5 0 1 1 11.2 3a7 7 0 0 0 9.8 9.8Z"/></svg>
</button>
```

```css
.theme { position: relative; width: 44px; height: 44px; border: 0; border-radius: 50%;
  background: var(--surface-2); color: var(--text); cursor: pointer; display: grid; place-items: center; }
.theme svg { position: absolute; width: 22px; height: 22px; fill: none; stroke: currentColor;
  stroke-width: 1.8; stroke-linecap: round; transition:
    transform var(--dur-slow) var(--ease-in-out),
    opacity var(--dur-fast) var(--ease-out); }
.theme__moon { opacity: 0; transform: rotate(-90deg) scale(.4); }
.theme[aria-pressed="true"] .theme__sun { opacity: 0; transform: rotate(90deg) scale(.4); }
.theme[aria-pressed="true"] .theme__moon { opacity: 1; transform: rotate(0) scale(1); }

/* Reveal layer (fallback when View Transitions is unsupported) */
::view-transition-old(root), ::view-transition-new(root) { animation: none; mix-blend-mode: normal; }
.theme-ripple {
  position: fixed; inset: 0; z-index: 9999; pointer-events: none;
  background: var(--surface); clip-path: circle(0 at var(--x) var(--y));
}
```

```js
export function initTheme(btn) {
  const root = document.documentElement;
  const setTheme = dark => {
    root.dataset.theme = dark ? 'dark' : 'light';
    btn.setAttribute('aria-pressed', String(dark));
    localStorage.setItem('theme', dark ? 'dark' : 'light');
  };

  btn.addEventListener('click', () => {
    const dark = btn.getAttribute('aria-pressed') !== 'true';
    // Expand from button center; keyboard trigger also gets the right center
    const r0 = btn.getBoundingClientRect();
    const x = r0.left + r0.width / 2;
    const y = r0.top + r0.height / 2;
    if (reduceMotion() || !document.startViewTransition) { setTheme(dark); return; }

    const r = Math.hypot(Math.max(x, innerWidth - x), Math.max(y, innerHeight - y));
    const t = document.startViewTransition(() => setTheme(dark));
    t.ready.then(() => {
      root.animate(
        { clipPath: [`circle(0 at ${x}px ${y}px)`, `circle(${r}px at ${x}px ${y}px)`] },
        { duration: 450, easing: 'cubic-bezier(.4,0,.2,1)',
          pseudoElement: '::view-transition-new(root)' }
      );
    });
  });

  // First load: read storage, then system; default to light if neither
  const stored = localStorage.getItem('theme');
  setTheme(stored ? stored === 'dark' : matchMedia('(prefers-color-scheme: dark)').matches);
}
```

Key points:
- Icon rotation uses `ease-in-out` 300ms, color transition 120ms; desync is intentional (shape slow, color fast).
- Reveal origin is the **button center** (not click point, not screen center); radius reaches farthest corner.
- Without `startViewTransition` (older Safari), switch theme instantly, or use a `.theme-ripple` overlay for a 400ms reveal.
- Use semantic tokens for theme values (`--surface` / `--text`); don't hard-cut between two color sets.

---

### 2.6 Copy feedback (icon to checkmark + tooltip + 2s reset)

```html
<button class="copy" type="button" data-copy="https://example.com">
  <svg class="copy__idle" viewBox="0 0 24 24"><rect x="9" y="9" width="11" height="11" rx="2"/><path d="M5 15V5a2 2 0 0 1 2-2h8"/></svg>
  <svg class="copy__done" viewBox="0 0 24 24"><path d="M5 13l4 4L19 7"/></svg>
  <span class="copy__tip" aria-hidden="true">Copied</span>
  <span class="copy__live sr-only" aria-live="polite"></span>
</button>
```

```css
.sr-only { position: absolute; width: 1px; height: 1px; padding: 0; margin: -1px;
  overflow: hidden; clip: rect(0 0 0 0); white-space: nowrap; border: 0; }
.copy { position: relative; width: 44px; height: 44px; border: 0; background: transparent;
  color: var(--text-2); cursor: pointer; display: grid; place-items: center; }
.copy svg { position: absolute; width: 20px; height: 20px; fill: none; stroke: currentColor;
  stroke-width: 2; stroke-linecap: round; stroke-linejoin: round;
  transition: opacity var(--dur-fast) var(--ease-out), transform var(--dur-fast) var(--ease-out); }
.copy__done { opacity: 0; transform: scale(.6) rotate(-12deg); color: var(--success); }
.copy.is-copied .copy__idle { opacity: 0; transform: scale(.6); }
.copy.is-copied .copy__done { opacity: 1; transform: scale(1) rotate(0); }
.copy__tip {
  position: absolute; left: 50%; bottom: calc(100% + 8px); translate: -50% 4px;
  padding: 6px 10px; border-radius: 8px; background: var(--tooltip-bg, #111);
  color: var(--tooltip-text, #fff); font: 500 13px/1 system-ui, sans-serif; white-space: nowrap;
  opacity: 0; pointer-events: none;
  transition: opacity var(--dur-fast) var(--ease-out), translate var(--dur-base) var(--ease-out);
}
.copy.is-copied .copy__tip { opacity: 1; translate: -50% 0; }
```

```js
export function initCopy(btn) {
  const live = btn.querySelector('.copy__live');
  let timer = 0;
  const show = () => {
    clearTimeout(timer);
    btn.classList.add('is-copied');
    live.textContent = '';
    requestAnimationFrame(() => { live.textContent = 'Copied'; });
    timer = setTimeout(() => btn.classList.remove('is-copied'), 2000);
  };

  btn.addEventListener('click', async () => {
    const text = btn.dataset.copy || '';
    try {
      await navigator.clipboard.writeText(text);
    } catch {
      const ta = document.createElement('textarea');
      ta.value = text; ta.style.position = 'fixed'; ta.style.opacity = '0';
      document.body.append(ta); ta.select();
      document.execCommand('copy'); ta.remove();
    }
    show();
    haptic(6);
  });
}
```

Key points:
- Tooltip uses `opacity + translate`, 180–200ms ease-out; auto-reset after **2s**; repeated clicks reset the timer.
- `role="status"` lets screen readers announce "Copied"; don't rely on icon alone.
- Copy failure needs error feedback (red flash + "Copy failed"); never silently succeed.
- Tooltip must not cover the primary action; place below if space is tight, flip with `bottom`/`top` classes.

---

## 3. Sixteen UI effects

### 3.1 Cursor spotlight

```css
.spotlight { position: relative; overflow: hidden; }
.spotlight::before {
  content: ""; position: absolute; inset: 0; pointer-events: none; opacity: 0;
  background: radial-gradient(240px circle at var(--mx, 50%) var(--my, 50%),
              rgb(255 255 255 / .12), transparent 65%);
  transition: opacity var(--dur-base) var(--ease-out);
}
.spotlight:hover::before { opacity: 1; }
```

```js
export function initSpotlight(el) {
  if (!canHover() || reduceMotion()) return;
  let raf = 0, x = 0, y = 0;
  el.addEventListener('pointermove', e => {
    const r = el.getBoundingClientRect();
    x = e.clientX - r.left; y = e.clientY - r.top;
    if (!raf) raf = requestAnimationFrame(() => {
      el.style.setProperty('--mx', `${x}px`);
      el.style.setProperty('--my', `${y}px`);
      raf = 0;
    });
  });
}
```

Radius 180–320px, opacity ≤ .12; don't register on touch devices.

### 3.2 Scroll reveal (staggered)

```css
.reveal { opacity: 0; transform: translateY(22px);
  transition: opacity 500ms var(--ease-out), transform 500ms var(--ease-out);
  transition-delay: calc(var(--i, 0) * 32ms); }
.reveal.is-in { opacity: 1; transform: none; }
```

```js
export function initReveal(scope = document) {
  const items = scope.querySelectorAll('.reveal');
  if (reduceMotion() || !('IntersectionObserver' in window)) {
    items.forEach(el => el.classList.add('is-in')); return;
  }
  const io = new IntersectionObserver(entries => {
    for (const en of entries) if (en.isIntersecting) {
      en.target.classList.add('is-in'); io.unobserve(en.target);
    }
  }, { rootMargin: '0px 0px -10% 0px', threshold: .12 });
  items.forEach(el => io.observe(el));
}
```

20–40ms per item, total ≤ 500ms; hidden-by-default must have a no-JS fallback (`<noscript>` or add a `js` class first).

### 3.3 Edge sheen

```css
@property --angle { syntax: "<angle>"; initial-value: 0deg; inherits: false; }
.edge { position: relative; border-radius: 16px; background: var(--surface); }
.edge::before {
  content: ""; position: absolute; inset: 0; border-radius: inherit; padding: 1px;
  background: conic-gradient(from var(--angle), transparent 55%, var(--accent), transparent 75%);
  -webkit-mask: linear-gradient(#000 0 0) content-box, linear-gradient(#000 0 0);
  -webkit-mask-composite: xor; mask-composite: exclude;
  animation: edge-spin 4s linear infinite;
}
@keyframes edge-spin { to { --angle: 360deg; } }
@media (prefers-reduced-motion: reduce) { .edge::before { animation: none; opacity: .35; } }
```

`@property` makes the angle interpolable; fall back to a static gradient when unsupported. 3–5s linear loop.

### 3.4 Magnetic button

```js
export function initMagnetic(el, strength = .28, max = 8) {
  if (!canHover() || reduceMotion()) return;
  el.addEventListener('pointermove', e => {
    el.style.transition = 'none';                 // No transition while tracking pointer
    const r = el.getBoundingClientRect();
    const dx = (e.clientX - r.left - r.width / 2) * strength;
    const dy = (e.clientY - r.top - r.height / 2) * strength;
    el.style.transform = `translate(${Math.max(-max, Math.min(max, dx))}px,
                                     ${Math.max(-max, Math.min(max, dy))}px)`;
  });
  el.addEventListener('pointerleave', () => {
    el.style.transition = 'transform 260ms cubic-bezier(.34,1.56,.64,1)';
    el.style.transform = 'translate(0,0)';
  });
}
```

Max displacement 6–10px; 260ms slight overshoot on leave; disable on touch.

### 3.5 Scroll narrative

```html
<section class="story">
  <div class="story__sticky">
    <h2 class="story__title">Configure in three steps</h2>
    <div class="story__stage">
      <article class="story__act" data-act="1">Connect data source</article>
      <article class="story__act" data-act="2">Choose metrics</article>
      <article class="story__act" data-act="3">Publish dashboard</article>
    </div>
  </div>
</section>
```

```css
.story { height: 300vh; }
.story__sticky { position: sticky; top: 0; height: 100vh; display: grid; place-items: center; }
.story__act { opacity: .15; transform: scale(.96);
  transition: opacity 240ms var(--ease-out), transform 240ms var(--ease-out); }
.story__act.is-active { opacity: 1; transform: scale(1); }
```

```js
export function initStory(section) {
  if (reduceMotion()) return section.querySelectorAll('.story__act')
    .forEach(el => el.classList.add('is-active'));
  const acts = [...section.querySelectorAll('.story__act')];
  const update = () => {
    const r = section.getBoundingClientRect();
    const total = r.height - innerHeight;
    const p = Math.min(Math.max(-r.top / total, 0), .999);
    const idx = Math.floor(p * acts.length);
    acts.forEach((el, i) => el.classList.toggle('is-active', i === idx));
  };
  addEventListener('scroll', () => requestAnimationFrame(update), { passive: true });
  update();
}
```

1–1.5 viewports per act; CSS `animation-timeline: scroll()` enables a zero-JS version; all visible without JS.

### 3.6 3D tilt

```js
export function initTilt(el, max = 8) {
  if (!canHover() || reduceMotion()) return;
  el.style.transformStyle = 'preserve-3d';
  el.addEventListener('pointermove', e => {
    el.style.transition = 'none';
    const r = el.getBoundingClientRect();
    const px = (e.clientX - r.left) / r.width - .5;
    const py = (e.clientY - r.top) / r.height - .5;
    el.style.transform = `perspective(1000px) rotateX(${-py * max}deg) rotateY(${px * max}deg)`;
  });
  el.addEventListener('pointerleave', () => {
    el.style.transition = 'transform 200ms cubic-bezier(0,0,.2,1)';
    el.style.transform = 'perspective(1000px)';
  });
}
```

Max 6–10°, perspective 800–1200; change transform only.

### 3.7 Text decode

```js
export function decodeText(el, finalText, duration = 700) {
  if (reduceMotion()) { el.textContent = finalText; return; }
  const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789#%&';
  const start = performance.now();
  const tick = now => {
    const p = Math.min((now - start) / duration, 1);
    const keep = Math.floor(p * finalText.length);
    el.textContent = finalText.slice(0, keep) +
      Array.from({ length: finalText.length - keep },
        (_, i) => finalText[keep + i] === ' ' ? ' ' : chars[(Math.random() * chars.length) | 0]).join('');
    if (p < 1) requestAnimationFrame(tick);
    else el.textContent = finalText;
  };
  requestAnimationFrame(tick);
}
```

Only for short headings (< 30 chars); update text once per frame; give screen readers the final copy via `aria-label`.

### 3.8 Shine sweep

```css
.shine { position: relative; overflow: hidden; }
.shine::after {
  content: ""; position: absolute; inset: 0; pointer-events: none;
  background: linear-gradient(105deg, transparent 35%, rgb(255 255 255 / .35) 50%, transparent 65%);
  transform: translateX(-150%);
}
.shine:hover::after { animation: shine 600ms var(--ease-in-out); }
@keyframes shine { to { transform: translateX(150%); } }
```

600ms, interval ≥ 2s; off under reduce-motion.

### 3.9 Parallax

```js
export function initParallax(layer, rate = .15) {
  if (reduceMotion()) return;
  let raf = 0;
  const update = () => {
    layer.style.transform = `translate3d(0, ${scrollY * rate}px, 0)`;
    raf = 0;
  };
  addEventListener('scroll', () => { if (!raf) raf = requestAnimationFrame(update); }, { passive: true });
}
```

Rate delta 0.1–0.3, displacement ≤ 15% viewport; disable on mobile or use CSS scroll-timeline.

### 3.10 Count up

```js
export function countUp(el, target, duration = 900) {
  const fmt = new Intl.NumberFormat('zh-CN');
  if (reduceMotion()) { el.textContent = fmt.format(target); return; }
  const start = performance.now();
  const tick = now => {
    const p = Math.min((now - start) / duration, 1);
    const eased = 1 - Math.pow(2, -10 * p);        // easeOutExpo
    el.textContent = fmt.format(Math.round(target * eased));
    if (p < 1) requestAnimationFrame(tick);
  };
  requestAnimationFrame(tick);
}
```

Start only when entering viewport; `font-variant-numeric: tabular-nums`; 800–1200ms.

### 3.11 Hover lift

```css
.lift { transition: transform 160ms var(--ease-out), box-shadow 160ms var(--ease-out); }
.lift:hover { transform: translateY(-3px); box-shadow: 0 8px 24px rgb(0 0 0 / .12); }
.lift:active { transform: translateY(-1px); }
```

Use two preset shadow levels; never animate blur radius per frame.

### 3.12 Typewriter

```js
export async function typewriter(el, text, ms = 45) {
  if (reduceMotion()) { el.textContent = text; return; }
  el.textContent = '';
  el.classList.add('is-typing');
  for (const ch of text) {
    el.textContent += ch;
    await new Promise(r => setTimeout(r, ms));
  }
  el.classList.remove('is-typing');
}
```

```css
.is-typing::after { content: "▍"; animation: blink 1s linear infinite; }
@keyframes blink { 50% { opacity: 0; } }
```

30–60ms per char; short phrases only; provide skip.

### 3.13 Stagger

```css
.stagger > * { opacity: 0; transform: translateY(12px);
  animation: stagger-in 400ms var(--ease-out) forwards;
  animation-delay: calc(var(--i, 0) * 30ms); }
@keyframes stagger-in { to { opacity: 1; transform: none; } }
```

Set `style="--i:0"`… in HTML; batch beyond 20 items; total ≤ 500ms.

### 3.14 Marquee

```html
<div class="marquee" aria-label="Announcement">
  <div class="marquee__track">
    <span>¥20 off first order · Free shipping sitewide · 7-day returns · </span>
    <span aria-hidden="true">¥20 off first order · Free shipping sitewide · 7-day returns · </span>
  </div>
</div>
```

```css
.marquee { overflow: hidden; }
.marquee__track { display: flex; width: max-content; animation: marquee 26s linear infinite; }
.marquee:hover .marquee__track, .marquee:focus-within .marquee__track { animation-play-state: paused; }
@keyframes marquee { to { transform: translateX(-50%); } }
@media (prefers-reduced-motion: reduce) { .marquee { overflow-x: auto; } .marquee__track { animation: none; } }
```

Duplicate content for a seamless loop; 20–40s per cycle; must be pausable.

### 3.15 Spring

```css
.pop { animation: pop 360ms cubic-bezier(.34,1.56,.64,1); }
@keyframes pop {
  0% { transform: scale(1); }
  45% { transform: scale(1.12); }
  100% { transform: scale(1); }
}
```

Only for likes, badges, success icons; never for layout, progress, or page transitions.

### 3.16 Easing comparison

```html
<div class="ease-demo">
  <div class="dot ease-linear"></div>
  <div class="dot ease-in"></div>
  <div class="dot ease-out"></div>
  <div class="dot ease-in-out"></div>
</div>
```

```css
.ease-demo .dot { width: 24px; height: 24px; border-radius: 50%; background: var(--accent);
  animation: slide 1.6s infinite alternate; }
.ease-linear  { animation-timing-function: linear; }
.ease-in      { animation-timing-function: cubic-bezier(.4,0,1,1); }
.ease-out     { animation-timing-function: cubic-bezier(0,0,.2,1); }
.ease-in-out  { animation-timing-function: cubic-bezier(.4,0,.2,1); }
@keyframes slide { to { transform: translateX(240px); } }
```

For team alignment and QA; don't stack other motion on top in product.

---

## 4. Performance and fallback checks

| Check | Practice | Failure sign |
| --- | --- | --- |
| Animate compositor properties only | Continuous animation uses only `transform`/`opacity`; progress with `scaleX` | Sustained Layout / Paint in Performance |
| Don't read layout | Don't read `offsetWidth` in scroll/pointer callbacks; cache rect first | Dropped frames, jitter |
| Promote sparingly | Add `will-change` before animation, remove on `animationend` | High GPU memory, blurry page |
| Cap particles | DOM ≤ 80, mobile ≤ 40, use Canvas beyond | Jank on low-end devices |
| Frame budget | JS < 8ms per frame; 60fps = 16.7ms/frame | Long tasks > 50ms |
| Cleanup | WAAPI `onfinish` removes nodes; IO `unobserve` | Memory growth, stacked animations |
| Fallback | reduce-motion, no hover, no View Transitions all have static equivalents | Broken function or lost information |

FLIP (for layout changes):

```js
export function flip(el, mutate) {
  const first = el.getBoundingClientRect();
  mutate();
  const last = el.getBoundingClientRect();
  const dx = first.left - last.left, dy = first.top - last.top;
  const sx = first.width / last.width, sy = first.height / last.height;
  el.animate(
    [{ transform: `translate(${dx}px,${dy}px) scale(${sx},${sy})` }, { transform: 'none' }],
    { duration: 260, easing: 'cubic-bezier(.4,0,.2,1)' }
  );
}
```

---

## 5. Duration quick reference

| Effect | Duration | Curve |
| --- | --- | --- |
| Press, icon swap | 80–120ms | ease-out |
| Hover, tooltip, copy toast | 120–200ms | ease-out |
| Buttons, switches, like count | 200–240ms | ease-out |
| Card/overlay enter & exit | 200–280ms | enter ease-out / exit ease-in |
| In-page shared element, slider | 220–320ms | ease-in-out |
| Page transition, theme reveal | 300–450ms | ease-in-out |
| List stagger | 20–40ms/item, total ≤ 500ms | ease-out |
| Progress, marquee, spin | by cycle | linear |
| Spring/bounce | 300–450ms | spring |

---

## 6. Platform equivalents

### SwiftUI

```swift
// Easing: .easeIn (exit) / .easeOut (enter) / .easeInOut (movement) / .linear (progress)
withAnimation(.easeOut(duration: 0.2)) { isOn.toggle() }
withAnimation(.spring(response: 0.35, dampingFraction: 0.7)) { liked.toggle() }

// Hold-to-confirm progress
@State private var progress: CGFloat = 0
Circle()
  .trim(from: 0, to: progress)
  .stroke(Color.accentColor, style: StrokeStyle(lineWidth: 2, lineCap: .round))
  .animation(.linear(duration: 0.7), value: progress)

// Success haptic
.sensoryFeedback(.success, trigger: didConfirm)

// Reduce motion
@Environment(\.accessibilityReduceMotion) private var reduceMotion
.animation(reduceMotion ? nil : .easeOut(duration: 0.2), value: isOn)
```

### Jetpack Compose

```kotlin
// Curves: LinearEasing / FastOutLinearInEasing (enter?) / LinearOutSlowInEasing / FastOutSlowInEasing
// Use LinearOutSlowInEasing (ease-out) to enter, FastOutLinearInEasing (ease-in) to exit
val alpha by animateFloatAsState(
    targetValue = if (visible) 1f else 0f,
    animationSpec = tween(200, easing = LinearOutSlowInEasing),
    label = "alpha"
)
val scale by animateFloatAsState(
    targetValue = if (pressed) .96f else 1f,
    animationSpec = spring(dampingRatio = 0.7f, stiffness = 500f),
    label = "press"
)

// Haptics
val haptic = LocalHapticFeedback.current
haptic.performHapticFeedback(HapticFeedbackType.LongPress)

// System animation scale is 0 (reduce motion)
val scale = LocalMotionDurationScale.current.scaleFactor // switch state directly when 0f
```

### UIKit

```swift
UIView.animate(withDuration: 0.2, delay: 0,
               options: [.curveEaseOut, .allowUserInteraction]) {
    self.card.alpha = 1
    self.card.transform = .identity
}
// Exit: .curveEaseIn; movement: .curveEaseInOut; constant: .curveLinear
UIImpactFeedbackGenerator(style: .light).impactOccurred()
```

---

## 7. Pre-delivery motion checklist (10 items)

1. Every animation explains a state change; pure decoration is removed.
2. Enter ease-out, exit ease-in, movement ease-in-out, constant speed linear.
3. Exit is shorter than enter; UI animation ≤ 400ms.
4. Continuous animation changes only `transform` / `opacity`; no sustained Layout/Paint.
5. Progress bars/sliders use `scaleX`/`translateX`, not `width`.
6. Feedback within 100ms of press; success and failure are distinguishable.
7. Animation is interruptible, repeatable, and non-blocking for clicks and scroll.
8. Under `prefers-reduced-motion`, a static equivalent exists with no information loss.
9. Haptics only on success/key confirmation; no vibration on failure.
10. End state = static style; don't rely on `fill-mode: forwards` permanently.
