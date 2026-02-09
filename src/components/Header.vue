<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';
import dcLogo from '../assets/images/dc_logo.png';

const menuOpen = ref(false);

const navGroups = [
  { label: 'Discover', links: [{ href: '#vision', label: 'Our Vision' }, { href: '#about', label: 'About Us' }] },
  { label: 'Connect', links: [{ href: '#leadership', label: 'Leadership' }, { href: '#connect', label: 'Join Us' }] },
];

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
});
onUnmounted(() => {
  document.removeEventListener('keydown', handleKeydown);
});
</script>

<template>
  <header class="relative z-50 bg-white text-[rgb(var(--dc-ink))] shadow-md">
    <div class="mx-auto flex max-w-[75rem] items-center justify-between gap-4 px-[var(--section-padding-x)] py-4">
      <a
        href="/"
        class="flex items-center gap-2 focus:outline-none focus:ring-2 focus:ring-dc-blue focus:ring-offset-2 focus:ring-offset-white rounded"
        aria-label="Dominion City Canary Wharf – Home"
      >
        <img
          :src="dcLogo.src"
          alt=""
          class="h-9 w-auto"
          width="40"
          height="36"
        />
        <span class="text-lg font-semibold tracking-tight text-dc-blue">Dominion City Canary Wharf</span>
      </a>

      <nav class="hidden md:flex md:items-center md:gap-8" aria-label="Main">
        <template v-for="(group, gi) in navGroups" :key="group.label">
          <div v-if="gi > 0" class="w-px h-5 bg-[rgb(var(--dc-ink))]/20" aria-hidden="true" />
          <div class="flex items-center gap-6">
            <span class="text-xs uppercase tracking-wider text-[rgb(var(--dc-ink))]/60 font-semibold">{{ group.label }}</span>
            <a
              v-for="link in group.links"
              :key="link.href"
              :href="link.href"
              class="text-[rgb(var(--dc-ink))]/90 hover:text-dc-blue font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-dc-blue focus:ring-offset-2 focus:ring-offset-white rounded"
            >
              {{ link.label }}
            </a>
          </div>
        </template>
      </nav>

      <button
        type="button"
        class="flex h-10 w-10 items-center justify-center rounded-md md:hidden text-[rgb(var(--dc-ink))] hover:bg-[rgb(var(--dc-ink))]/5 focus:outline-none focus:ring-2 focus:ring-dc-blue focus:ring-offset-2 focus:ring-offset-white"
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
      class="md:hidden border-t border-[rgb(var(--dc-ink))]/10 bg-[rgb(var(--dc-cream))]"
      :class="menuOpen ? 'block' : 'hidden'"
      role="dialog"
      aria-label="Mobile menu"
    >
      <nav class="flex flex-col gap-1 px-[var(--section-padding-x)] py-4" aria-label="Main">
        <template v-for="group in navGroups" :key="group.label">
          <p class="text-xs uppercase tracking-wider text-[rgb(var(--dc-ink))]/60 font-semibold mt-3 mb-1 first:mt-0">{{ group.label }}</p>
          <a
            v-for="link in group.links"
            :key="link.href"
            :href="link.href"
            class="rounded-md px-3 py-2.5 text-[rgb(var(--dc-ink))]/90 hover:bg-white hover:text-dc-blue font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-dc-blue focus:ring-inset"
            @click="closeMenu"
          >
            {{ link.label }}
          </a>
        </template>
      </nav>
    </div>
  </header>
</template>
