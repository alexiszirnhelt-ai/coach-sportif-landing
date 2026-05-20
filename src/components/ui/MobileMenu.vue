<script setup lang="ts">
import { ref, watch, onUnmounted } from "vue";

interface NavLink {
  label: string;
  href: string;
}

defineProps<{
  links: readonly NavLink[];
}>();

const isOpen = ref(false);

function open() {
  isOpen.value = true;
}

function close() {
  isOpen.value = false;
}

// Bloque le scroll du body quand le menu est ouvert
watch(isOpen, (newValue) => {
  if (typeof document === "undefined") return;
  document.body.style.overflow = newValue ? "hidden" : "";
});

// Nettoyage si le composant est démonté alors que le menu est ouvert
onUnmounted(() => {
  if (typeof document === "undefined") return;
  document.body.style.overflow = "";
});
</script>

<template>
  <!-- Bouton burger (visible uniquement sur mobile) -->
  <button
    type="button"
    class="flex h-10 w-10 items-center justify-center rounded-lg border border-neutral-800 text-neutral-100 transition-colors hover:bg-neutral-900 md:hidden"
    :class="{ 'mobile-menu-open': isOpen }"
    aria-label="Ouvrir le menu"
    @click="open"
  >
    <svg
      xmlns="http://www.w3.org/2000/svg"
      class="h-5 w-5"
      fill="none"
      viewBox="0 0 24 24"
      stroke="currentColor"
      stroke-width="2"
    >
      <path
        stroke-linecap="round"
        stroke-linejoin="round"
        d="M3.75 6.75h16.5M3.75 12h16.5m-16.5 5.25h16.5"
      />
    </svg>
  </button>

  <!-- Overlay plein écran -->
  <transition
    enter-active-class="transition-opacity duration-300"
    leave-active-class="transition-opacity duration-200"
    enter-from-class="opacity-0"
    enter-to-class="opacity-100"
    leave-from-class="opacity-100"
    leave-to-class="opacity-0"
  >
    <div
      v-if="isOpen"
      class="fixed inset-0 z-50 bg-neutral-950 md:hidden"
      role="dialog"
      aria-modal="true"
      aria-label="Menu principal"
    >
      <div class="flex h-full flex-col">
        <!-- Header de l'overlay : logo + croix -->
        <div
          class="flex items-center justify-between border-b border-neutral-800 px-6 py-4"
        >
          <a
            href="/"
            class="flex items-center"
            aria-label="Jane Doe — Accueil"
            @click="close"
          >
            <img
              src="/logo.svg"
              alt="Jane Doe Coaching"
              class="h-20 w-auto"
              width="80"
              height="80"
            />
          </a>

          <button
            type="button"
            class="flex h-10 w-10 items-center justify-center rounded-lg border border-neutral-800 text-neutral-100 transition-colors hover:bg-neutral-900"
            aria-label="Fermer le menu"
            @click="close"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              class="h-5 w-5"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
              stroke-width="2"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M6 18 18 6M6 6l12 12"
              />
            </svg>
          </button>
        </div>

        <!-- Liens de navigation -->
        <nav class="flex flex-1 flex-col justify-center px-6">
          <ul class="space-y-2">
            <li v-for="link in links" :key="link.href">
              <a
                :href="link.href"
                class="block rounded-xl px-4 py-4 text-2xl font-medium text-neutral-100 transition-colors hover:bg-neutral-900"
                @click="close"
              >
                {{ link.label }}
              </a>
            </li>
          </ul>

          <!-- CTA en bas -->
          <a
            href="#contact"
            class="mt-8 inline-flex items-center justify-center rounded-full bg-neutral-100 px-6 py-3 text-sm font-medium text-neutral-900 transition-colors hover:bg-white"
            @click="close"
          >
            Réserver
          </a>
        </nav>
      </div>
    </div>
  </transition>
</template>
