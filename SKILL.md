---
name: jakub-design-engineering
description: "Design engineering principles, UI polish techniques, animation patterns, CSS/Motion best practices distilled from Jakub Krehel's writings (jakub.kr). Use when implementing animations, building UI interactions, working with gradients/colors, or reviewing interface quality. Triggers on: animation, motion, framer-motion, gesture, drag, transition, gradient, OKLCH, will-change, layout animation, interface polish, UI details."
---

# Jakub Krehel — Design Engineering Knowledge Base
Source: https://jakub.kr | @jakubkrehel
Scraped: 2026-03-10 | 10 articles

---

## 1. Details That Make Interfaces Feel Better
Source: https://jakub.kr/writing/details-that-make-interfaces-feel-better

### text-wrap: balance
Prevents orphaned words. One line, huge improvement on headings/captions.
```css
p, h1, h2 { text-wrap: balance; }
/* text-wrap: pretty — similar, slightly slower algorithm */
```

### Concentric Border Radius ← APPLY ALWAYS
**outer-radius = inner-radius + padding**
Nested card: inner 12px + padding 8px = outer 20px. Most apps ship this wrong.
```css
.outer { border-radius: 20px; padding: 8px; }
.inner { border-radius: 12px; } /* 20 - 8 = 12 ✓ */
```

### Icon Transitions (opacity + scale + blur)
Animate icon swaps (copy → check) with opacity/scale/blur. Use Motion for springs.
```tsx
<motion.div key={isCopied ? "check" : "copy"} animate={{ opacity: 1, scale: 1, filter: "blur(0px)" }} initial={{ opacity: 0, scale: 0.8, filter: "blur(4px)" }}>
  {isCopied ? <CheckIcon /> : <CopyIcon />}
</motion.div>
```

### Font Smoothing — Always
```html
<body class="antialiased"> <!-- Tailwind -->
```
```css
body { -webkit-font-smoothing: antialiased; }
```
Prevents heavy macOS text rendering. No excuse not to have this.

### Tabular Numbers
Prevents layout shift when digits update. Some fonts (Inter) change numeral appearance.
```css
.number { font-variant-numeric: tabular-nums; }
/* Tailwind: tabular-nums */
```

### CSS Transitions vs Keyframe Animations
| Use | When |
|-----|------|
| **CSS transitions** | User interactions — interruptible, retarget to latest state |
| **Keyframe animations** | Staged one-shot sequences — fixed timeline |

Rule: If a user can interrupt it mid-animation, use transitions.

### Staggered Enter Animations
Animate elements individually with 80-100ms delay — NOT one big block.
```css
@keyframes enter {
  from { transform: translateY(8px); filter: blur(5px); opacity: 0; }
}
.animate-enter {
  animation: enter 800ms cubic-bezier(0.25, 0.46, 0.45, 0.94) both;
  animation-delay: calc(var(--delay, 0ms) * var(--stagger, 0));
}
/* Apply: --stagger: 1 (title), 2 (desc), 3 (buttons) */
```

### Subtle Exit Animations
Exits should be MORE subtle than enters — less attention, less movement.
```tsx
// Enter: full distance
initial={{ opacity: 0, y: "calc(-100% - 4px)", filter: "blur(4px)" }}
// Exit: subtle fixed value
exit={{ opacity: 0, y: "-12px", filter: "blur(4px)" }}
transition={{ type: "spring", duration: 0.45, bounce: 0 }}
```

### Optical Alignment
Geometric ≠ visual. Reduce padding on icon side of text+icon buttons. Fix in SVG when possible.

### Box Shadow > Border
Adapts to any background, adds depth. Use 3-layer shadow stack.
```css
.card {
  box-shadow:
    0px 0px 0px 1px rgba(0, 0, 0, 0.06),
    0px 1px 2px -1px rgba(0, 0, 0, 0.06),
    0px 2px 4px 0px rgba(0, 0, 0, 0.04);
}
/* Hover: same, slightly darker opacity. Transition with transition-[box-shadow] */
```

### Image Border Overlay
1px outline at 10% opacity — depth + consistent edges.
```css
.image {
  outline: 1px solid rgba(0, 0, 0, 0.1);
  outline-offset: -1px;
}
.dark .image { outline-color: rgba(255, 255, 255, 0.1); }
```

---

## 2. Using Gestures in Motion (framer-motion)
Source: https://jakub.kr/work/motion-gestures

Motion extends React events with: `whileHover`, `whileTap`, `onPan`, `whileDrag`, `whileFocus`, `whileInView`

**Performance:** For transform/clipPath/filter, Motion and CSS are identical — both run on compositor thread. Motion sometimes better due to independent transform handling.

```tsx
// Hover — spring (preferred over CSS for natural feel)
<motion.div whileHover={{ rotate: 180 }} transition={{ type: "spring", duration: 0.8, bounce: 0 }} />

// Tap — keyboard accessible (Return key triggers it)
<motion.div whileTap={{ scale: 0.8 }} transition={{ type: "spring", duration: 0.5, bounce: 0 }} />

// Drag with constraints
<motion.div whileDrag={{ scale: 0.8 }} drag dragConstraints={constraintsRef} />

// Pan — no while- prop, use handlers
<motion.div onPanStart={() => setIsPanning(true)} onPanEnd={() => setIsPanning(false)} />

// Focus — keep subtle, don't disorient keyboard users
<motion.button whileFocus={{ scale: 1.1 }} transition={{ type: "spring", duration: 0.3, bounce: 0 }} />

// InView — pair with initial for reveal animations
<motion.div initial={{ filter: "blur(8px)" }} whileInView={{ filter: "blur(0px)" }} viewport={{ amount: 0.5 }} />
```

---

## 3. Drag Gestures on the Web
Source: https://jakub.kr/work/drag-gesture

iOS-style swipe row with progressive reveal. Key implementation details:

```tsx
const x = useMotionValue(0);

<motion.div
  drag="x"
  dragConstraints={{ left: SNAP_POINTS.LEFT, right: SNAP_POINTS.RIGHT }}
  dragElastic={0.05}       // Low = tight boundaries
  dragMomentum={false}     // CRITICAL: row stops where you decide, not velocity
  style={{ x }}
/>
```

**Snap points:**
```tsx
const SNAP_POINTS = { LEFT: -116, CENTER: 0, RIGHT: 116 };
const POSITION_THRESHOLD = 58; // commit point

const handleDragEnd = () => {
  const currentX = x.get();
  animate(x,
    Math.abs(currentX) > POSITION_THRESHOLD
      ? (currentX > 0 ? SNAP_POINTS.RIGHT : SNAP_POINTS.LEFT)
      : SNAP_POINTS.CENTER,
    SPRING_CONFIG
  );
};
```

**Progressive reveal** — actions appear at specific thresholds:
```tsx
const [dragProgress, setDragProgress] = useState(0);
useEffect(() => x.on("change", setDragProgress), [x]);

<ActionButton isVisible={dragProgress > 44} />  // First action at 44px
<ActionButton isVisible={dragProgress > 88} />  // Second action at 88px
```

---

## 4. Using AI as a Design Engineer
Source: https://jakub.kr/work/using-ai-as-a-design-engineer

### Key Principles
- **AI accelerates workflow, doesn't replace thinking.** Use for tedious/time-consuming tasks.
- **Always understand what the agent does.** Don't blindly follow output.
- **Start every conversation assuming zero context.** Leads to best results.
- **Less code = better.** AI is great at finding duplications and extracting shared components.

### Agent Rules Setup (Cursor)
Keep rules short, specific, referencing other files. Categories:
- `animation-performance.mdc` — will-change, transform/opacity only
- `cn-util.mdc` — use cn() not template literals  
- `design-system.mdc` — component usage
- `animation-rules.mdc` — spring configs, timing

Resources: ui-skills.com, Vercel Web Interface Guidelines, Motion Plus rules, TailwindCSS sponsor rules

### /deslop Command
```
Check diff against main. Remove all AI slop: extra comments, defensive try/catch, any casts, 
inconsistent style. Report 1-3 sentence summary.
```

### Workflow
- Use **Plan mode** for bigger features — structured prompts > one big prompt
- Break prompts into sequential tasks: "Do task 1, then Do task 2"
- Figma MCP for UI scaffolding — output is surprisingly good with good rules
- context7 MCP for up-to-date library docs

### Model Recommendation (at time of writing)
Claude Opus 4.5 as daily driver for design engineering tasks.

### Code Review
Codex on every PR + Vercel agent for bigger PRs. Extra layer of review is always worth it.

---

## 5. will-change in CSS
Source: https://jakub.kr/components/will-change-in-css

A hint to the browser: "I'm about to animate these properties, get ready."

**Browser rendering pipeline:**
1. Layout (CPU) — box sizing and positioning
2. Paint (CPU) — fill pixels, colors, borders
3. Composite (GPU) — stack layers, show on screen

If animation only touches `transform`/`opacity` → browser can skip layout + paint, GPU handles everything.

**Without will-change:** layer promotion happens when animation starts → stutter on first frame
**With will-change:** pre-promotes on idle → smooth from frame 1

```css
/* Good — specific, intentional */
.animated-button { will-change: transform, opacity; }

/* Bad — too broad, wastes memory */
* { will-change: auto; }
.button { will-change: all; }
```

**Properties worth using will-change on:**
- `transform`, `opacity`, `filter` (blur/brightness) — GPU compositing
- `clip-path`, `mask` — modern browsers handle well
- `scroll-position` — for scroll animations
- `contents` — virtual lists, frequently updating containers

**Properties NOT worth it:** top, background, border — legal but no speed gain, just wastes memory.

Will-change is NOT a magic switch. Modern browsers already optimize well. Use sparingly and intentionally.

---

## 6. Shared Layout Animations (Motion)
Source: https://jakub.kr/work/shared-layout-animations

Animate between completely different elements with a single `layoutId`. Uses FLIP technique under the hood.

```tsx
// Same layoutId = Motion animates between them automatically
<motion.div layoutId="circle" className="size-16 rounded-full" />

// In different tab panels — Motion handles the transition
case "Design":
  return <motion.div layoutId="circle" className="..." />;
case "Engineering":
  return <motion.div layoutId="circle" className="absolute top-4 right-4 ..." />;
```

**Multiple elements:** as long as layoutId is unique to the group.

**With AnimatePresence:** keep `layoutId` elements OUTSIDE of `<AnimatePresence>`. If inside, initial/exit animations trigger simultaneously with layout animation — looks bad.

```tsx
// Correct pattern
<motion.div layoutId="card" transition={{ type: "spring", duration: 0.55, bounce: 0.1 }} />
<AnimatePresence mode="popLayout">
  <motion.div key={activeTab} animate={{ opacity: 1 }} exit={{ opacity: 0 }}>
    {getContent()}
  </motion.div>
</AnimatePresence>
```

Works with Radix Dialog — card expands into modal using shared layoutId.

---

## 7. Understanding Gradients (CSS Deep Dive)
Source: https://jakub.kr/work/gradients

### Three Types
```css
linear-gradient(to bottom, blue, red)
radial-gradient(circle, blue, red)
conic-gradient(from 90deg, blue, red)
```

### Linear — Key Options
```css
linear-gradient(45deg, blue, red)                    /* direction */
linear-gradient(to bottom, blue 25%, red 75%)        /* color stops */
linear-gradient(to bottom, blue 50%, red 50%)        /* sharp edge */
repeating-linear-gradient(45deg, blue 0 10px, red 10px 20px)
```

**Diamond gradient** (not a CSS type — 4 layered linears):
```css
background:
  linear-gradient(to bottom right, #fff 0%, #999 50%) bottom right / 50% 50% no-repeat,
  linear-gradient(to bottom left, #fff 0%, #999 50%) bottom left / 50% 50% no-repeat,
  linear-gradient(to top left, #fff 0%, #999 50%) top left / 50% 50% no-repeat,
  linear-gradient(to top right, #fff 0%, #999 50%) top right / 50% 50% no-repeat;
```

### Radial — Key Options
```css
radial-gradient(circle, blue, red)                    /* shape: circle | ellipse */
radial-gradient(circle closest-side, blue, red)       /* size keywords */
radial-gradient(circle at 70% 30%, blue, red)         /* position */
```

### Conic
```css
conic-gradient(from 90deg, blue, red)                 /* starting angle */
conic-gradient(at 70% 30%, blue, red)                 /* rotation center */
repeating-conic-gradient(from 0deg, #000 0deg 20deg, #fff 20deg 40deg)
```

### Color Spaces for Gradients
```css
linear-gradient(in oklch, blue, red)   /* CSS */
/* Tailwind 4 (defaults to oklab): */
<div class="bg-linear-to-r/oklch from-red-500 to-blue-500" />
```
- **sRGB** — default, can have muddy midpoints
- **oklab** — straight-line interpolation, consistent results (Tailwind 4 default)
- **oklch** — circular interpolation, can introduce unexpected colors (hue is angular)

### Color Hints (midpoint control)
```css
linear-gradient(to right, red, 40%, blue)  /* blend appears at 40% */
/* NOT a new color stop — changes rate of interpolation */
```

### Animation Performance
Cheap to animate: `background-position`, `background-size`, `opacity`
Expensive: changing color stops, adding/removing layers, switching types.
**Better approach:** animate a pseudo-element overlay or use CSS variables.

### Layered Gradients + Blend Modes
```css
.layered {
  background:
    linear-gradient(in oklch 135deg, oklch(0.75 0.18 280), oklch(0.8 0.15 80)),
    conic-gradient(in oklch from 180deg, oklch(0.7 0.12 200 / 0.3), oklch(0.7 0.12 340 / 0.3));
  background-blend-mode: overlay, color-dodge;
}
```

---

## 8. What Are OKLCH Colors?
Source: https://jakub.kr/components/oklch-colors

`oklch(lightness chroma hue)` — perceptually uniform color model.

- **Lightness** — 0 to 1 (or 0%-100%)
- **Chroma** — color intensity (like saturation)
- **Hue** — 0-360 degrees

### Why OKLCH Over HSL/RGB
```tsx
// Same L+C, just change hue → visually uniform buttons
oklch(0.55 0.18 30)   // orange
oklch(0.55 0.18 130)  // green
oklch(0.55 0.18 230)  // blue
oklch(0.55 0.18 300)  // purple

// HSL at same saturation — NOT visually uniform (some lighter, some darker)
```

**Shade generation** — oklch maintains consistent hue across shades. HSL drifts (blue → purple at light end, gray at dark end).

### Display P3
OKLCH can reference Display-P3 colors (wider gamut than sRGB). Supported on modern Apple displays.

### Browser Support + Fallbacks
```css
:root {
  --color-primary: #1E90FF;  /* sRGB fallback */
  @supports (color: oklch(0 0 0)) {
    --color-primary: oklch(0.55 0.18 260);
  }
}
```

### Tailwind 4
Defaults to oklab for gradient interpolation (better than sRGB, avoids oklch's hue detour).

### Tool: oklch.fyi
Generate palettes, convert existing CSS variables to oklch.

---

## Quick Reference Card

| Topic | Key Rule |
|-------|----------|
| Border radius | outer = inner + padding |
| Text wrap | `text-wrap: balance` on all headings |
| Font smoothing | `antialiased` on body, always |
| Transitions | User interactions = CSS transitions (interruptible) |
| Keyframes | One-shot sequences only |
| Enter animations | Stagger 80-100ms per element |
| Exit animations | Subtle — fixed -12px, not full distance |
| Box shadow | 3-layer stack > border |
| will-change | Specific + sparingly. `transform, opacity` only |
| OKLCH | Same L+C, change hue = perceptually uniform palette |
| Drag gestures | `dragMomentum={false}` for snap points |
| Layout animations | `layoutId` outside `<AnimatePresence>` |
| Gradient interpolation | oklab for consistency, oklch for creative effects |
| Image borders | `outline: 1px solid rgba(0,0,0,0.1); outline-offset: -1px` |
