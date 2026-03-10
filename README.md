# interface-feel

An OpenClaw skill distilled from [Jakub Krehel's](https://jakub.kr) writings on design engineering.

Jakub is a founding design engineer at Interfere, previously at OpenSea. His work covers the intersection of motion, CSS craft, and building interfaces that feel right.

---

## What's Inside

10 articles condensed into a single reference skill:

| Article | Topics |
|---------|--------|
| [Details That Make Interfaces Feel Better](https://jakub.kr/writing/details-that-make-interfaces-feel-better) | Concentric radius, text-wrap, icon transitions, box shadows, staggered animations, exits |
| [Using Gestures in Motion](https://jakub.kr/work/motion-gestures) | whileHover, whileTap, whileDrag, whileFocus, whileInView |
| [Drag Gestures on the Web](https://jakub.kr/work/drag-gesture) | iOS swipe rows, snap points, progressive reveal, dragMomentum |
| [Using AI as a Design Engineer](https://jakub.kr/work/using-ai-as-a-design-engineer) | Rules files, /deslop, Figma MCP, prompt structure, model workflow |
| [will-change in CSS](https://jakub.kr/components/will-change-in-css) | GPU compositing, when to use/not use, which properties matter |
| [Shared Layout Animations](https://jakub.kr/work/shared-layout-animations) | layoutId, FLIP, AnimatePresence gotcha, card → modal pattern |
| [Understanding Gradients](https://jakub.kr/work/gradients) | Linear/radial/conic, color hints, blend modes, animation perf |
| [What Are OKLCH Colors?](https://jakub.kr/components/oklch-colors) | Perceptually uniform palettes, sRGB vs P3, oklab vs oklch |
| [Infinite Card Stack](https://jakub.kr/work/infinite-card-stack) | Animated card stack component |
| [Clip Path Buttons](https://jakub.kr/work/clip-path-buttons) | Hold-effect buttons with contextual icon switching |

---

## Installation

### OpenClaw
```bash
# Copy SKILL.md to your skills directory
cp SKILL.md ~/.openclaw/skills/jakub-design-engineering/SKILL.md
```

Or via [ClawHub](https://clawhub.com) once published there.

### Any AI Agent
The `SKILL.md` is written as a plain markdown knowledge base — drop it into your agent's context, system prompt, or rules directory. Works with Cursor, Claude Code, Codex, or any tool that accepts context files.

---

## Usage

The skill auto-activates on tasks involving:
- Animation, motion, framer-motion, gestures
- UI polish, interface details, microinteractions
- Gradients, OKLCH colors, color palettes
- will-change, CSS performance
- Shared layout animations, drag interactions
- Design engineering workflow / AI tooling

### Quick Reference (from the skill)

| Topic | Key Rule |
|-------|----------|
| Border radius | `outer = inner + padding` (concentric) |
| Text wrap | `text-wrap: balance` on all headings |
| Font smoothing | `antialiased` on body, always |
| Transitions | User interactions = CSS transitions (interruptible) |
| Keyframes | One-shot sequences only |
| Enter animations | Stagger 80-100ms per element |
| Exit animations | Subtle — fixed -12px, not full distance |
| Box shadow | 3-layer stack beats a border |
| will-change | Specific + sparingly. `transform, opacity` only |
| OKLCH | Same L+C, change hue = perceptually uniform palette |
| Drag gestures | `dragMomentum={false}` for snap points |
| Layout animations | `layoutId` outside `<AnimatePresence>` |
| Gradient interpolation | oklab for consistency, oklch for creative effects |
| Image borders | `outline: 1px solid rgba(0,0,0,0.1); outline-offset: -1px` |

---

## Credits

All content is sourced from [Jakub Krehel](https://jakub.kr) — this repo is just a structured reference format. Go read his actual articles for the interactive demos.

Follow him: [@jakubkrehel](https://x.com/jakubkrehel)

---

## Contributing

If Jakub publishes new articles, PRs to add them to `SKILL.md` are welcome. Keep the same format — concise, code-first, no filler.
