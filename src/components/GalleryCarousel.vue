<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue';

interface GalleryImage {
  src: string;
  alt: string;
}

const props = withDefaults(
  defineProps<{ images?: GalleryImage[] }>(),
  { images: () => [] }
);

const slides = computed(() => (props.images?.length ? props.images : []));
const current = ref(0);
let autoplayTimer: ReturnType<typeof setInterval> | null = null;
const AUTOPLAY_MS = 5000;

function goTo(index: number) {
  if (!slides.value.length) return;
  current.value = (index + slides.value.length) % slides.value.length;
  resetAutoplay();
}

function next() {
  goTo(current.value + 1);
}

function prev() {
  goTo(current.value - 1);
}

function resetAutoplay() {
  if (autoplayTimer) clearInterval(autoplayTimer);
  if (slides.value.length > 1) {
    autoplayTimer = setInterval(next, AUTOPLAY_MS);
  }
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
    v-if="slides.length"
    class="relative w-full overflow-hidden rounded-2xl bg-[rgb(var(--dc-ink))]/5"
    aria-label="Photo gallery carousel"
  >
    <div class="relative aspect-[16/10] sm:aspect-[21/9] w-full">
      <div
        v-for="(image, index) in slides"
        :key="index"
        class="absolute inset-0 transition-opacity duration-500"
        :class="index === current ? 'opacity-100 z-0' : 'opacity-0 z-0 pointer-events-none'"
        :aria-hidden="index !== current"
      >
        <img
          :src="image.src"
          :alt="image.alt"
          class="w-full h-full object-cover"
          loading="lazy"
          width="800"
          height="450"
        />
      </div>

      <button
        v-if="slides.length > 1"
        type="button"
        class="absolute left-2 top-1/2 z-10 -translate-y-1/2 rounded-full p-2 bg-white/90 text-[rgb(var(--dc-ink))] shadow-md hover:bg-white focus:outline-none focus:ring-2 focus:ring-dc-blue focus:ring-offset-2 transition-colors"
        aria-label="Previous photo"
        @click="prev"
      >
        <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
        </svg>
      </button>
      <button
        v-if="slides.length > 1"
        type="button"
        class="absolute right-2 top-1/2 z-10 -translate-y-1/2 rounded-full p-2 bg-white/90 text-[rgb(var(--dc-ink))] shadow-md hover:bg-white focus:outline-none focus:ring-2 focus:ring-dc-blue focus:ring-offset-2 transition-colors"
        aria-label="Next photo"
        @click="next"
      >
        <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
        </svg>
      </button>

      <div
        v-if="slides.length > 1"
        class="absolute bottom-4 left-1/2 z-10 flex -translate-x-1/2 gap-2"
        role="tablist"
        aria-label="Gallery navigation"
      >
        <button
          v-for="(_, index) in slides"
          :key="index"
          type="button"
          class="h-2 w-2 rounded-full transition-colors focus:outline-none focus:ring-2 focus:ring-white focus:ring-offset-2 focus:ring-offset-[rgb(var(--dc-ink))]/30"
          :class="index === current ? 'bg-white w-6' : 'bg-white/60 hover:bg-white/80 w-2'"
          :aria-label="`Go to photo ${index + 1}`"
          :aria-selected="index === current"
          role="tab"
          @click="goTo(index)"
        />
      </div>
    </div>
  </section>
</template>
