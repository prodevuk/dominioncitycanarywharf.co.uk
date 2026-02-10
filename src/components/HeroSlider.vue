<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue';

const props = withDefaults(
  defineProps<{ yearOfEagleSrc?: string }>(),
  { yearOfEagleSrc: '' }
);

const slides = computed(() => [
  {
    type: 'text',
    eyebrow: 'Dominion City · Canary Wharf',
    title: 'We are Dominion City',
    subtitle: 'Raising Leaders that transform society',
    location: 'Canary Wharf, London',
  },
]);

const current = ref(0);
let autoplayTimer: ReturnType<typeof setInterval> | null = null;
const AUTOPLAY_MS = 6000;

function goTo(index: number) {
  current.value = (index + slides.value.length) % slides.value.length;
  resetAutoplay();
}

function next() {
  goTo(current.value + 1);
}

const slidesLength = computed(() => slides.value.length);

function prev() {
  goTo(current.value - 1);
}

function resetAutoplay() {
  if (autoplayTimer) clearInterval(autoplayTimer);
  autoplayTimer = setInterval(next, AUTOPLAY_MS);
}

onMounted(() => {
  resetAutoplay();
});
onUnmounted(() => {
  if (autoplayTimer) clearInterval(autoplayTimer);
});
</script>

<template>
  <section
    class="relative min-h-[85vh] flex flex-col items-center justify-center bg-dc-blue text-white overflow-hidden"
    aria-label="Hero slider"
  >
    <!-- Slides -->
    <div class="absolute inset-0 flex">
      <div
        v-for="(slide, index) in slides"
        :key="index"
        class="absolute inset-0 flex flex-col items-center justify-center transition-opacity duration-500"
        :class="[
          index === current ? 'opacity-100 z-0' : 'opacity-0 z-0 pointer-events-none',
          slide.type === 'image' ? 'p-0' : 'px-[var(--section-padding-x)] py-16 text-center',
        ]"
        :aria-hidden="index !== current"
        :role="index === current ? 'group' : undefined"
        :aria-label="`Slide ${index + 1} of ${slidesLength}`"
      >
        <!-- Text slide -->
        <template v-if="slide.type === 'text'">
          <p class="text-sm uppercase tracking-[0.2em] text-white/80 mb-4">
            {{ slide.eyebrow }}
          </p>
          <h1 class="text-4xl font-bold tracking-tight sm:text-5xl md:text-6xl max-w-3xl mx-auto leading-tight">
            {{ slide.title }}
          </h1>
          <p class="mt-6 text-xl sm:text-2xl text-white/95 font-medium max-w-2xl mx-auto">
            {{ slide.subtitle }}
          </p>
          <p class="mt-4 text-white/80">
            {{ slide.location }}
          </p>
          <div class="mt-10 flex flex-wrap items-center justify-center gap-4">
            <a
              href="#vision"
              class="inline-flex items-center justify-center rounded-md bg-white px-6 py-3 text-base font-semibold text-dc-blue shadow-sm hover:bg-white/95 focus:outline-none focus:ring-2 focus:ring-white focus:ring-offset-2 focus:ring-offset-[rgb(30,58,138)] transition-colors"
            >
              Our Vision
            </a>
            <a
              href="#connect"
              class="inline-flex items-center justify-center rounded-md border-2 border-white px-6 py-3 text-base font-semibold text-white hover:bg-white/10 focus:outline-none focus:ring-2 focus:ring-white focus:ring-offset-2 focus:ring-offset-[rgb(30,58,138)] transition-colors"
            >
              Join Us
            </a>
          </div>
        </template>
        <!-- Image slide (Year of the Eagle) -->
        <template v-else-if="slide.type === 'image' && slide.image">
          <img
            :src="slide.image"
            :alt="slide.alt"
            class="absolute inset-0 h-full w-full object-cover object-center"
            width="1920"
            height="1080"
            fetchpriority="low"
          />
          <div class="absolute bottom-20 left-1/2 z-10 -translate-x-1/2 flex flex-wrap items-center justify-center gap-4">
            <a
              href="#vision"
              class="inline-flex items-center justify-center rounded-md bg-white px-6 py-3 text-base font-semibold text-dc-blue shadow-sm hover:bg-white/95 focus:outline-none focus:ring-2 focus:ring-white focus:ring-offset-2 focus:ring-offset-[rgb(30,58,138)] transition-colors"
            >
              Our Vision
            </a>
            <a
              href="#connect"
              class="inline-flex items-center justify-center rounded-md border-2 border-white px-6 py-3 text-base font-semibold text-white hover:bg-white/10 focus:outline-none focus:ring-2 focus:ring-white focus:ring-offset-2 focus:ring-offset-[rgb(30,58,138)] transition-colors"
            >
              Join Us
            </a>
          </div>
        </template>
      </div>
    </div>

    <!-- Prev / Next (hidden when only one slide) -->
    <template v-if="slidesLength > 1">
      <button
        type="button"
        class="absolute left-2 top-1/2 z-10 -translate-y-1/2 rounded-full p-2 text-white/90 hover:bg-white/15 hover:text-white focus:outline-none focus:ring-2 focus:ring-white focus:ring-offset-2 focus:ring-offset-[rgb(30,58,138)] transition-colors"
        aria-label="Previous slide"
        @click="prev"
      >
        <svg class="h-8 w-8" fill="none" stroke="currentColor" viewBox="0 0 24 24" aria-hidden="true">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
        </svg>
      </button>
      <button
        type="button"
        class="absolute right-2 top-1/2 z-10 -translate-y-1/2 rounded-full p-2 text-white/90 hover:bg-white/15 hover:text-white focus:outline-none focus:ring-2 focus:ring-white focus:ring-offset-2 focus:ring-offset-[rgb(30,58,138)] transition-colors"
        aria-label="Next slide"
        @click="next"
      >
        <svg class="h-8 w-8" fill="none" stroke="currentColor" viewBox="0 0 24 24" aria-hidden="true">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
        </svg>
      </button>

      <!-- Dots -->
      <div
        class="absolute bottom-8 left-1/2 z-10 flex -translate-x-1/2 gap-2"
        role="tablist"
        aria-label="Slide navigation"
      >
        <button
          v-for="(_, index) in slides"
          :key="index"
          type="button"
          class="h-2.5 w-2.5 rounded-full transition-colors focus:outline-none focus:ring-2 focus:ring-white focus:ring-offset-2 focus:ring-offset-[rgb(30,58,138)]"
          :class="index === current ? 'bg-white' : 'bg-white/50 hover:bg-white/70'"
          :aria-label="`Go to slide ${index + 1}`"
          :aria-selected="index === current"
          role="tab"
          @click="goTo(index)"
        />
      </div>
    </template>
  </section>
</template>
