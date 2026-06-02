# Effects Reference

Every visual effect used in this design system — what it is, how it works, and the core code behind it.

---

## Background & Atmosphere

### Aurora Sweep
A slow-rotating teal conic-gradient that sits behind the entire page, giving it a living, breathing quality.

**How it works:** CSS `@property` registers a custom `<angle>` type, which can then be animated in a `@keyframes` block — something impossible with regular CSS variables.

```css
@property --aurora-a { syntax: '<angle>'; initial-value: 0deg; inherits: false; }

.aurora {
  position: fixed; inset: 0;
  background: conic-gradient(from var(--aurora-a) at 25% 55%,
    transparent, rgba(6,182,212,.055) 15%, transparent 42%);
  animation: aurora-spin 22s linear infinite;
}
@keyframes aurora-spin { to { --aurora-a: 360deg; } }
```

---

### Particle Network
A canvas-based field of floating dots connected by lines when close together. Particles drift toward the cursor.

**How it works:** Each tick, every particle checks its distance to the cursor and adds a small velocity nudge toward it. A nested loop draws lines between any two particles closer than `MAX_DIST`.

```js
particles.forEach(p => {
  const dx = mx - p.x, dy = my - p.y;
  const d  = Math.hypot(dx, dy);
  if (d < 200) { p.vx += dx / d * .018; p.vy += dy / d * .018; }
});

// Draw connections
for (let i = 0; i < particles.length; i++)
  for (let j = i + 1; j < particles.length; j++) {
    const d = Math.hypot(particles[i].x - particles[j].x, particles[i].y - particles[j].y);
    if (d < 130) {
      ctx.strokeStyle = `rgba(6,182,212,${.18 * (1 - d / 130)})`;
      ctx.beginPath();
      ctx.moveTo(particles[i].x, particles[i].y);
      ctx.lineTo(particles[j].x, particles[j].y);
      ctx.stroke();
    }
  }
```

---

### Cursor Spotlight
A teal radial glow that follows the mouse across the entire page, illuminating content under the cursor.

**How it works:** `mousemove` updates two CSS custom properties (`--mx`, `--my`) on a fixed overlay element. The radial gradient repaints at the new position each frame.

```js
document.addEventListener('mousemove', e => {
  spotlight.style.setProperty('--mx', e.clientX + 'px');
  spotlight.style.setProperty('--my', e.clientY + 'px');
});
```
```css
#spotlight {
  position: fixed; inset: 0; pointer-events: none;
  background: radial-gradient(700px circle at var(--mx) var(--my),
    rgba(6,182,212,.1) 0%, transparent 65%);
}
```

---

## Cursor

### Custom Cursor (Dot + Lagging Ring)
The native cursor is hidden. A small teal dot snaps instantly to mouse position. A larger ring lags behind using linear interpolation.

**How it works:** The dot is positioned directly. The ring uses a `requestAnimationFrame` loop where it moves 10% of the remaining distance to the target each frame — creating smooth, natural lag.

```js
// Dot — instant
cur.style.left = mx + 'px';
cur.style.top  = my + 'px';

// Ring — lerp each frame
(function loop() {
  rx += (mx - rx) * .1;
  ry += (my - ry) * .1;
  ring.style.left = rx + 'px';
  ring.style.top  = ry + 'px';
  requestAnimationFrame(loop);
})();
```

---

## Card Effects

### Running Border
A glowing teal streak orbits the edge of a card on hover. Uses the CSS `@property` trick to animate a `conic-gradient` angle.

**How it works:** The card wrapper gets a `conic-gradient` pseudo-element sized slightly larger than the card. `@property` registers the angle as an animatable type, so `@keyframes` can sweep it 360°. The card itself masks the center, leaving only the border strip visible.

```css
@property --a { syntax: '<angle>'; initial-value: 0deg; inherits: false; }

.card-outer::before {
  content: ''; position: absolute; inset: -3px;
  background: conic-gradient(from var(--a),
    transparent 50%,
    rgba(34,211,238,.3) 63%, #22D3EE 73%,
    rgba(255,255,255,.9) 78%, #06B6D4 84%,
    transparent 95%);
  animation: rot 2s linear infinite paused;
  opacity: 0; transition: opacity .35s;
}
@keyframes rot { to { --a: 360deg; } }
.card-outer:hover::before { opacity: 1; animation-play-state: running; }
```

---

### 3D Card Tilt
Cards rotate in perspective tracking the mouse position, with a specular highlight that moves to simulate a light source.

**How it works:** On `mousemove`, the cursor's position within the card is normalized to `-0.5 → 0.5`. These values drive `rotateX` and `rotateY`. A radial gradient pseudo-element repositions to match, simulating a reflection.

```js
wrap.addEventListener('mousemove', e => {
  const rect = wrap.getBoundingClientRect();
  const x = (e.clientX - rect.left) / rect.width  - .5;
  const y = (e.clientY - rect.top)  / rect.height - .5;
  card.style.transform = `rotateY(${x * 18}deg) rotateX(${-y * 18}deg) scale(1.03)`;
  specular.style.setProperty('--cx', ((x + .5) * 100) + '%');
  specular.style.setProperty('--cy', ((y + .5) * 100) + '%');
});
wrap.addEventListener('mouseleave', () => {
  card.style.transform = '';
  card.style.transition = 'transform .55s cubic-bezier(.34, 1.56, .64, 1)';
});
```

---

### Lift & Shadow Bloom
Cards float upward on hover with a teal-tinted shadow that expands outward.

**How it works:** CSS `translateY` + `scale` with a spring cubic-bezier easing (`cubic-bezier(.34, 1.56, .64, 1)`) creates overshoot — the card rises slightly past its resting position before settling.

```css
.card:hover {
  transform: translateY(-14px) scale(1.04);
  box-shadow:
    0 28px 60px rgba(6,182,212,.2),
    0 0 0 1px rgba(6,182,212,.15);
  border-color: var(--teal);
}
```

---

### Shimmer Sweep
A semi-transparent light band slides across the card surface on hover, simulating a reflection.

**How it works:** A pseudo-element starts at `left: -100%` and animates to `left: 160%` — passing through the card once. It's triggered by the hover state on the parent.

```css
.card::after {
  content: ''; position: absolute;
  top: 0; left: -100%; width: 55%; height: 100%;
  background: linear-gradient(105deg, transparent, rgba(255,255,255,.4), transparent);
}
.card:hover::after { animation: shimmer .6s ease forwards; }
@keyframes shimmer { to { left: 160%; } }
```

---

### Glassmorphism
A frosted-glass surface — translucent, blurred, with a teal tint.

**How it works:** `backdrop-filter: blur()` blurs whatever is behind the element. Combined with a semi-transparent background and a subtle border, it creates depth.

```css
.card-glass {
  background: rgba(240,253,254,.75);
  border: 1px solid rgba(6,182,212,.2);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
}
```

---

## Button & Interaction Effects

### Magnetic Pull
Buttons physically drift toward the cursor when hovered, then spring back on leave.

**How it works:** `mousemove` on the button calculates the cursor's offset from the button's center and translates by a fraction of that distance. On `mouseleave`, a spring easing snaps it back.

```js
btn.addEventListener('mousemove', e => {
  const r = btn.getBoundingClientRect();
  const x = (e.clientX - r.left - r.width  / 2) * .38;
  const y = (e.clientY - r.top  - r.height / 2) * .38;
  btn.style.transform  = `translate(${x}px, ${y}px)`;
  btn.style.transition = 'transform .12s';
});
btn.addEventListener('mouseleave', () => {
  btn.style.transform  = '';
  btn.style.transition = 'transform .55s cubic-bezier(.34, 1.56, .64, 1)';
});
```

---

### Click Ripple
A circle expands from the exact click point, simulating a physical ripple.

**How it works:** A `<span>` is created at the click coordinates (relative to the button), sized to cover the button, then scaled from `0` to `4` with opacity fading out. It removes itself after the animation completes.

```js
btn.addEventListener('click', e => {
  const span = document.createElement('span');
  const size = Math.max(btn.offsetWidth, btn.offsetHeight) * 2;
  const rect = btn.getBoundingClientRect();
  span.style.cssText = `
    width: ${size}px; height: ${size}px;
    left: ${e.clientX - rect.left - size/2}px;
    top:  ${e.clientY - rect.top  - size/2}px;
    position: absolute; border-radius: 50%;
    background: rgba(255,255,255,.3);
    transform: scale(0);
    animation: ripple-out .55s linear;
  `;
  btn.appendChild(span);
  setTimeout(() => span.remove(), 600);
});
```

---

## Typography Effects

### Text Scramble
Letters cycle through random characters before resolving to the final string — like a decryption animation.

**How it works:** A `requestAnimationFrame` loop runs for a fixed duration. For each character position, if it's past the "resolved" threshold (based on elapsed time), the final character is shown. Otherwise a random character from a pool is drawn.

```js
function scramble(el, final, duration) {
  const CHARS = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
  let start = null;
  function frame(ts) {
    if (!start) start = ts;
    const progress  = Math.min((ts - start) / duration, 1);
    const resolved  = Math.floor(progress * final.length);
    let out = '';
    for (let i = 0; i < final.length; i++) {
      if (final[i] === ' ') { out += ' '; continue; }
      out += i < resolved
        ? final[i]
        : CHARS[Math.floor(Math.random() * CHARS.length)];
    }
    el.textContent = out;
    if (progress < 1) requestAnimationFrame(frame);
  }
  requestAnimationFrame(frame);
}
```

---

### Gradient Text Fill
A teal-to-cyan gradient color slides across the text on trigger.

**How it works:** The gradient is set to `200%` wide and positioned at the right (hidden). Animating `background-position` to `0%` slides the teal into view. `background-clip: text` clips it to the character shapes.

```css
.heading {
  background: linear-gradient(90deg, #0A0F1E 0%, #0891B2 40%, #22D3EE 60%, #0A0F1E 100%);
  background-size: 200% auto;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  background-position: 100% center;
}
.heading.active {
  animation: grad-slide 1.8s ease forwards;
}
@keyframes grad-slide { to { background-position: 0% center; } }
```

---

### Highlight Sweep
A teal-tinted block sweeps from left to right under text, like a highlighter marker.

**How it works:** A pseudo-element sits behind the text (`z-index: 0`), initially `scaleX(0)` from the left edge. On trigger, it scales to `1` with a spring ease.

```css
.wrap { position: relative; display: inline-block; }
.wrap::after {
  content: ''; position: absolute;
  bottom: 3px; left: 0; right: 0; height: 38%;
  background: var(--teal-pale); border-radius: 3px;
  z-index: 0;
  transform: scaleX(0); transform-origin: left;
  transition: transform .55s cubic-bezier(.4, 0, .2, 1);
}
.wrap:hover::after { transform: scaleX(1); }
```

---

### Word Stagger
Words enter one by one with an increasing delay, cascading across the line.

**How it works:** The text content is split by spaces, each word wrapped in a `<span>` with an inline `transition-delay` proportional to its index. Adding a class triggers the transition on all spans simultaneously — but each fires at a different time.

```js
const words = el.textContent.split(' ');
el.innerHTML = words.map((w, i) =>
  `<span style="display:inline-block; opacity:0; transform:translateY(10px);
               transition: opacity .3s ${i * 70}ms, transform .3s ${i * 70}ms">
    ${w}&nbsp;
  </span>`
).join('');
```

---

### Blur-In Reveal
Text emerges from a blurry state to sharp focus, combined with a fade-in.

**How it works:** The element starts with `filter: blur(8px)` and `opacity: 0`. Both CSS properties transition simultaneously on trigger. The blur resolves slightly before the opacity completes, giving a sense of the content "materialising".

```css
.el { filter: blur(8px); opacity: 0; transition: filter .7s ease, opacity .7s ease; }
.el.active { filter: blur(0); opacity: 1; }
```

---

### Typewriter
Characters appear one by one at a variable speed, mimicking human typing.

**How it works:** A `setInterval` (or recursive `setTimeout`) appends one character per tick. A small random jitter on the delay `36 + Math.random() * 18` makes it feel organic.

```js
let i = 0;
function type() {
  if (i < text.length) {
    el.textContent += text[i++];
    setTimeout(type, 36 + Math.random() * 18);
  }
}
type();
```

---

## Scroll & Viewport Effects

### Scroll Reveal (Fade Up)
Elements start invisible and below their natural position, then animate into place as they enter the viewport.

**How it works:** `IntersectionObserver` watches every `.fade-up` element. When one crosses the threshold, a class is added that transitions `opacity` and `translateY`. A stagger is added by multiplying the callback entry index by a delay.

```js
const obs = new IntersectionObserver(entries => {
  entries.forEach((entry, i) => {
    if (entry.isIntersecting) {
      setTimeout(() => entry.target.classList.add('in'), i * 60);
      obs.unobserve(entry.target);
    }
  });
}, { threshold: .08 });

document.querySelectorAll('.fade-up').forEach(el => obs.observe(el));
```
```css
.fade-up { opacity: 0; transform: translateY(24px); transition: opacity .55s ease, transform .55s cubic-bezier(.4,0,.2,1); }
.fade-up.in { opacity: 1; transform: none; }
```

---

### Animated Counter
Numbers count up from zero when the element enters the viewport.

**How it works:** `IntersectionObserver` fires the animation once on enter. A `requestAnimationFrame` loop calculates elapsed time as a 0–1 value, applies a cubic ease-out, and multiplies by the target number.

```js
function animateCounter(el, target, duration = 1400) {
  const start = performance.now();
  function tick(now) {
    const t    = Math.min((now - start) / duration, 1);
    const ease = 1 - Math.pow(1 - t, 3); // cubic ease-out
    el.textContent = Math.round(ease * target);
    if (t < 1) requestAnimationFrame(tick);
  }
  requestAnimationFrame(tick);
}
```

---

### Progress Bar Reveal
Progress bars animate from `width: 0` to their target width when scrolled into view, with a moving shine effect.

**How it works:** Bars start at `width: 0%`. `IntersectionObserver` fires a `setTimeout` cascade per bar, then sets `transition` and applies the target width. A shimmer `@keyframes` runs continuously on top.

```js
obs = new IntersectionObserver(entries => {
  entries.forEach(e => {
    if (e.isIntersecting) {
      e.target.querySelectorAll('.progress-bar').forEach((bar, i) => {
        setTimeout(() => {
          bar.style.transition = 'width 1.1s cubic-bezier(.4,0,.2,1)';
          bar.style.width = bar.dataset.width + '%';
        }, i * 80);
      });
    }
  });
}, { threshold: .3 });
```

---

## Miscellaneous

### Skeleton Wave
A moving gradient simulates content that is loading, replacing static grey blocks.

**How it works:** Instead of a pulse animation, `background-position` is animated across a 200%-wide gradient. The gradient moves left → right continuously, creating a travelling shine effect.

```css
.sk {
  background: linear-gradient(90deg, #F1F5F9 25%, #E2E8F0 50%, #F1F5F9 75%);
  background-size: 200% 100%;
  animation: sk-wave 1.6s ease-in-out infinite;
}
@keyframes sk-wave {
  0%   { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}
```

---

### Badge Pulse
A subtle `box-shadow` ring expands and contracts around a badge, suggesting an active or live state.

**How it works:** A `@keyframes` loop transitions `box-shadow` from `0px spread` to `6px spread` and back. The shadow color is a low-opacity teal so it reads as a gentle halo.

```css
@keyframes badge-pulse {
  0%, 100% { box-shadow: 0 0 0 0   rgba(6,182,212, 0); }
  50%       { box-shadow: 0 0 0 6px rgba(6,182,212,.08); }
}
.badge { animation: badge-pulse 3s ease-in-out infinite; }
```

---

### Toggle Glow
When a toggle switch is turned on, a teal `box-shadow` appears to give the impression of an emitted light.

```css
.toggle.on {
  background: var(--teal);
  box-shadow: 0 0 12px rgba(6,182,212,.4);
}
```

---

### Avatar Spring Pop
Avatars in a stacked group lift and scale on hover with spring overshoot, and stack over adjacent avatars via `z-index`.

```css
.avatar {
  transition: transform .25s cubic-bezier(.34, 1.56, .64, 1), box-shadow .25s;
}
.avatar:hover {
  transform: translateY(-7px) scale(1.12);
  z-index: 10;
  box-shadow: 0 8px 20px rgba(0,0,0,.15);
}
```

---

### Section Card Running Border
Each section is wrapped in a card that reveals an orbiting teal border on hover — the same `@property` conic-gradient trick as the component cards, applied at the section level.

```css
@property --sc-a { syntax: '<angle>'; initial-value: 0deg; inherits: false; }

.section-card::before {
  background: conic-gradient(from var(--sc-a),
    #E5E7EB 0%, #E5E7EB 70%,
    rgba(34,211,238,.6) 80%, #06B6D4 85%, #E5E7EB 95%);
  animation: sc-spin 4s linear infinite paused;
}
.section-card:hover::before { animation-play-state: running; }
@keyframes sc-spin { to { --sc-a: 360deg; } }
```

---

## Spring Easing Reference

The cubic-bezier values used throughout this system:

| Name | Value | Character |
|---|---|---|
| Standard | `cubic-bezier(.4, 0, .2, 1)` | Smooth, Material-style |
| Spring (overshoot) | `cubic-bezier(.34, 1.56, .64, 1)` | Bounces past target, settles back |
| Snap in | `cubic-bezier(.4, 0, .2, 1)` | Fast in, gradual out |
| Ease out cubic | `1 - Math.pow(1-t, 3)` | JS counter/canvas animations |
