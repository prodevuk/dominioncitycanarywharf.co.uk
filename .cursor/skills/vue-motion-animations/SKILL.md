---
name: vue-motion-animations
description: Creates declarative animations in Vue using @vueuse/motion (Framer Motion–inspired). Use when building Vue animations, page transitions, scroll-triggered effects, or when the user mentions Framer Motion–style animations in Vue.
---

# Vue Motion Animations (@vueuse/motion)

## Setup

Install and register the plugin:

```bash
yarn add @vueuse/motion
```

In your Vue app entry (e.g. Astro Vue integration or `main.js`):

```js
import { createApp } from 'vue'
import { MotionPlugin } from '@vueuse/motion'
import App from './App.vue'

const app = createApp(App)
app.use(MotionPlugin)
app.mount('#app')
```

For Astro with Vue, register the plugin in the Vue app factory used by `@astrojs/vue`.

---

## Core: v-motion directive

Use the `v-motion` directive on any element. Variant props define when and how it animates.

### Variant props

| Prop | When it runs |
|------|----------------|
| `initial` | Before mount (starting state) |
| `enter` | After mount (animates from initial) |
| `visible` | When element enters viewport; reverts when leaving |
| `visible-once` | When element enters viewport (runs once) |
| `hovered` | On pointer hover |
| `focused` | On focus |
| `tapped` | On click/tap |

Use kebab-case in template: `:initial`, `:enter`, `:visible`, `:hovered`, `:tapped`, etc.

### Basic example

```vue
<template>
  <div
    v-motion
    :initial="{ opacity: 0, y: 20 }"
    :enter="{ opacity: 1, y: 0 }"
    :transition="{ duration: 400 }"
  >
    Hello
  </div>
</template>
```

### With delay and duration

```vue
<div
  v-motion
  :initial="{ opacity: 0, x: -20 }"
  :enter="{ opacity: 1, x: 0 }"
  :delay="200"
  :duration="600"
/>
```

---

## Presets

Presets are preconfigured directives. Use them for common effects without defining variants.

| Preset | Effect |
|--------|--------|
| `v-motion-fade` | Fade in on enter |
| `v-motion-fade-visible` | Fade in when in viewport |
| `v-motion-fade-visible-once` | Fade in once when in viewport |
| `v-motion-slide-*-visible` | Slide from top/bottom/left/right when visible |
| `v-motion-pop` | Pop/scale on enter |
| `v-motion-roll-*-visible` | Roll from edge when visible |

Example:

```vue
<div v-motion-slide-bottom-visible />
<div v-motion-fade :delay="100" />
```

---

## Stagger children

Use `v-motion` with the same variant name on parent and children, and set `stagger` on the parent so children animate in sequence.

```vue
<template>
  <ul
    v-motion
    :initial="{ opacity: 0 }"
    :enter="{ opacity: 1, transition: { staggerChildren: 0.1 } }"
  >
    <li
      v-for="(item, i) in items"
      :key="item.id"
      v-motion
      :initial="{ opacity: 0, x: -10 }"
      :enter="{ opacity: 1, x: 0 }"
    >
      {{ item.name }}
    </li>
  </ul>
</template>
```

---

## Gestures (hover, tap)

```vue
<template>
  <button
    v-motion
    :hovered="{ scale: 1.05 }"
    :tapped="{ scale: 0.98 }"
    :transition="{ type: 'spring', stiffness: 400, damping: 17 }"
  >
    Click me
  </button>
</template>
```

---

## useMotion composable

For imperative control or shared variant config, use `useMotion`:

```vue
<script setup>
import { useMotion } from '@vueuse/motion'

const { variant } = useMotion({
  initial: { opacity: 0 },
  enter: { opacity: 1 },
  visible: { opacity: 1, y: 0 },
  visibleOnce: { opacity: 1 }
})
</script>

<template>
  <div v-motion="variant">Content</div>
</template>
```

---

## Transition options

Use `transition` in a variant or as a top-level prop for timing:

```vue
<div
  v-motion
  :enter="{ opacity: 1, y: 0 }"
  :transition="{ duration: 500, ease: [0.22, 1, 0.36, 1] }"
/>
```

Spring:

```vue
:transition="{ type: 'spring', stiffness: 300, damping: 25 }"
```

---

## Checklist

When adding Vue motion:

- [ ] Plugin installed and registered in the Vue app
- [ ] Prefer `visible` or `visible-once` for scroll-triggered content
- [ ] Use presets when they match the effect; use raw `v-motion` for custom variants
- [ ] Use `staggerChildren` for list/item sequences
- [ ] Prefer spring for UI feedback (buttons, toggles); tween for enter/leave

## Additional resources

- Docs: [motion.vueuse.org](https://motion.vueuse.org/)
- Presets and API: [motion.vueuse.org/features/presets](https://motion.vueuse.org/features/presets)
