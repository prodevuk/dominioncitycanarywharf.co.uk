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
const currentPage = ref(0);

// 1-up on mobile, 2-up on small screens, 4-up on large screens.
const perView = ref(1);
function updatePerView() {
  // Tailwind breakpoints: sm=640px, lg=1024px
  const w = window.innerWidth;
  perView.value = w >= 1024 ? 4 : w >= 640 ? 2 : 1;
}

const totalPages = computed(() => {
  const count = slides.value.length;
  return count ? Math.ceil(count / perView.value) : 0;
});

const showPagination = computed(() => totalPages.value > 1);

let autoplayTimer: ReturnType<typeof setInterval> | null = null;
const AUTOPLAY_MS = 5000;

function clampPage(index: number) {
  const pages = totalPages.value;
  if (pages <= 1) return 0;
  return (index + pages) % pages;
}

function goToPage(index: number) {
  if (!slides.value.length) return;
  currentPage.value = clampPage(index);
  resetAutoplay();
}

function next() {
  goToPage(currentPage.value + 1);
}

function prev() {
  goToPage(currentPage.value - 1);
}

// Swipe support (mobile)
const touchStartX = ref<number | null>(null);
const touchDeltaX = ref(0);
const SWIPE_THRESHOLD_PX = 50;

function onTouchStart(event: TouchEvent) {
  if (!showPagination.value) return;
  if (event.touches.length !== 1) return;
  touchStartX.value = event.touches[0]?.clientX ?? null;
  touchDeltaX.value = 0;
}

function onTouchMove(event: TouchEvent) {
  if (touchStartX.value == null) return;
  if (event.touches.length !== 1) return;
  const x = event.touches[0]?.clientX;
  if (typeof x !== 'number') return;
  touchDeltaX.value = x - touchStartX.value;
}

function onTouchEnd() {
  if (touchStartX.value == null) return;
  const delta = touchDeltaX.value;
  touchStartX.value = null;
  touchDeltaX.value = 0;

  if (Math.abs(delta) < SWIPE_THRESHOLD_PX) return;
  if (delta > 0) prev();
  else next();
}

function resetAutoplay() {
  if (autoplayTimer) clearInterval(autoplayTimer);
  if (totalPages.value > 1) {
    autoplayTimer = setInterval(next, AUTOPLAY_MS);
  }
}

onMounted(() => {
  updatePerView();
  window.addEventListener('resize', updatePerView);
  // Ensure current page is in-range when perView changes
  currentPage.value = clampPage(currentPage.value);
  resetAutoplay();
});
onUnmounted(() => {
  window.removeEventListener('resize', updatePerView);
  if (autoplayTimer) clearInterval(autoplayTimer);
});
</script>

<template>
  <section
    v-if="slides.length"
    class="relative w-full overflow-hidden rounded-2xl bg-[rgb(var(--dc-ink))]/5"
    aria-label="Photo gallery carousel"
  >
    <div
      class="relative w-full select-none"
      @touchstart.passive="onTouchStart"
      @touchmove.passive="onTouchMove"
      @touchend.passive="onTouchEnd"
      @touchcancel.passive="onTouchEnd"
    >
      <!-- Track: 1-up on mobile, 2-up on sm, 4-up on lg -->
      <div class="relative overflow-hidden">
        <div
          class="flex transition-transform duration-500 ease-out"
          :style="{ transform: `translateX(-${currentPage * 100}%)` }"
          aria-live="polite"
        >
          <div
            v-for="(image, index) in slides"
            :key="index"
            class="shrink-0 w-full sm:w-1/2 lg:w-1/4 p-1.5 sm:p-2"
          >
            <div class="aspect-square w-full overflow-hidden rounded-xl bg-white/60">
              <img
                :src="image.src"
                :alt="image.alt"
                class="h-full w-full object-cover"
                loading="lazy"
                width="600"
                height="600"
              />
            </div>
          </div>
        </div>
      </div>

      <button
        v-if="showPagination"
        type="button"
        class="absolute left-2 sm:left-3 top-1/2 z-10 -translate-y-1/2 rounded-full p-2 sm:p-2.5 bg-white/90 text-[rgb(var(--dc-ink))] shadow-md hover:bg-white focus:outline-none focus:ring-2 focus:ring-dc-blue focus:ring-offset-2 transition-colors"
        aria-label="Previous photos"
        @click="prev"
      >
        <svg class="h-5 w-5 sm:h-6 sm:w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
        </svg>
      </button>
      <button
        v-if="showPagination"
        type="button"
        class="absolute right-2 sm:right-3 top-1/2 z-10 -translate-y-1/2 rounded-full p-2 sm:p-2.5 bg-white/90 text-[rgb(var(--dc-ink))] shadow-md hover:bg-white focus:outline-none focus:ring-2 focus:ring-dc-blue focus:ring-offset-2 transition-colors"
        aria-label="Next photos"
        @click="next"
      >
        <svg class="h-5 w-5 sm:h-6 sm:w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
        </svg>
      </button>

      <div
        v-if="showPagination"
        class="absolute bottom-3 sm:bottom-4 left-1/2 z-10 flex -translate-x-1/2 gap-2"
        role="tablist"
        aria-label="Gallery navigation"
      >
        <button
          v-for="(_, index) in Array.from({ length: totalPages })"
          :key="index"
          type="button"
          class="h-2 sm:h-2.5 rounded-full transition-colors focus:outline-none focus:ring-2 focus:ring-white focus:ring-offset-2 focus:ring-offset-[rgb(var(--dc-ink))]/30"
          :class="index === currentPage ? 'bg-white w-6' : 'bg-white/60 hover:bg-white/80 w-2'"
          :aria-label="`Go to page ${index + 1}`"
          :aria-selected="index === currentPage"
          role="tab"
          @click="goToPage(index)"
        />
      </div>
    </div>
  </section>
</template>
