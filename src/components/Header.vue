<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';
import dcLogo from '../assets/images/dc_logo.png';

const menuOpen = ref(false);
const scrolledPastHero = ref(false);

const navLinks = [
  { href: '#vision', label: 'Our Vision' },
  { href: '#about', label: 'About Us' },
  { href: '#leadership', label: 'Leadership' },
  { href: '#connect', label: 'Connect' },
];

function updateScrolledPastHero() {
  // Hero is min-h-[85vh]; switch header when user has scrolled past most of it
  const threshold = typeof window !== 'undefined' ? window.innerHeight * 0.8 : 0;
  scrolledPastHero.value = window.scrollY >= threshold;
}

function toggleMenu() {
  menuOpen.value = !menuOpen.value;
}

function closeMenu() {
  menuOpen.value = false;
}

function handleKeydown(e: KeyboardEvent) {
  if (e.key === 'Escape') closeMenu();
}

onMounted(() => {
  document.addEventListener('keydown', handleKeydown);
  updateScrolledPastHero();
  window.addEventListener('scroll', updateScrolledPastHero, { passive: true });
});
onUnmounted(() => {
  document.removeEventListener('keydown', handleKeydown);
  window.removeEventListener('scroll', updateScrolledPastHero);
});
</script>

<template>
  <header
    class="fixed top-0 left-0 right-0 z-50 text-white transition-[background-color,box-shadow] duration-200"
    :class="scrolledPastHero || menuOpen ? 'bg-dc-blue shadow-md' : 'bg-transparent shadow-none'"
  >
    <div class="mx-auto flex max-w-[75rem] items-center justify-between gap-4 px-[var(--section-padding-x)] py-4">
      <a
        href="/"
        class="flex items-center gap-2 focus:outline-none focus:ring-2 focus:ring-white focus:ring-offset-2 focus:ring-offset-[rgb(30,58,138)] rounded"
        aria-label="Dominion City Canary Wharf – Home"
      >
        <img
          :src="dcLogo.src"
          alt=""
          class="h-9 w-auto"
          width="40"
          height="36"
        />
        <span class="text-lg font-semibold tracking-tight">Dominion City Canary Wharf</span>
      </a>

      <nav class="hidden md:flex md:items-center md:gap-8" aria-label="Main">
        <a
          v-for="link in navLinks"
          :key="link.href"
          :href="link.href"
          class="text-white/95 hover:text-white font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-white focus:ring-offset-2 focus:ring-offset-[rgb(30,58,138)] rounded"
        >
          {{ link.label }}
        </a>
      </nav>

      <button
        type="button"
        class="flex h-10 w-10 items-center justify-center rounded-md md:hidden text-white hover:bg-white/10 focus:outline-none focus:ring-2 focus:ring-white"
        :aria-expanded="menuOpen"
        aria-controls="mobile-menu"
        aria-label="Toggle menu"
        @click="toggleMenu"
      >
        <span v-if="!menuOpen" class="sr-only">Open menu</span>
        <span v-else class="sr-only">Close menu</span>
        <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" aria-hidden="true">
          <path v-if="!menuOpen" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
          <path v-else stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
        </svg>
      </button>
    </div>

    <div
      id="mobile-menu"
      class="md:hidden border-t border-white/20 bg-dc-blue"
      :class="menuOpen ? 'block' : 'hidden'"
      role="dialog"
      aria-label="Mobile menu"
    >
      <nav class="flex flex-col gap-1 px-[var(--section-padding-x)] py-4" aria-label="Main">
        <a
          v-for="link in navLinks"
          :key="link.href"
          :href="link.href"
          class="rounded-md px-3 py-2.5 text-white/95 hover:bg-white/10 hover:text-white font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-white focus:ring-inset"
          @click="closeMenu"
        >
          {{ link.label }}
        </a>
      </nav>
    </div>
  </header>
</template>
