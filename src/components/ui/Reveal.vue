<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";

interface Props {
  /** Délai avant l'animation (en ms) */
  delay?: number;
  /** Distance de translation initiale (en px) */
  translateY?: number;
  /** Durée de l'animation (en ms) */
  duration?: number;
}

const { delay = 0, translateY = 20, duration = 700 } = defineProps<Props>();

const target = ref<HTMLElement | null>(null);
const isVisible = ref(false);

let observer: IntersectionObserver | null = null;

onMounted(() => {
  if (typeof window === "undefined" || !target.value) return;

  // Respecte la préférence utilisateur "réduire les animations"
  const prefersReducedMotion = window.matchMedia(
    "(prefers-reduced-motion: reduce)",
  ).matches;
  if (prefersReducedMotion) {
    isVisible.value = true;
    return;
  }

  observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          // L'élément est entré dans le viewport → on l'anime
          setTimeout(() => {
            isVisible.value = true;
          }, delay);

          // On arrête d'observer (animation jouée une seule fois)
          observer?.unobserve(entry.target);
        }
      });
    },
    {
      // L'animation se déclenche quand 15% de l'élément est visible
      threshold: 0.15,
      // Marge négative en bas : déclenche un peu avant que l'élément soit complètement entré
      rootMargin: "0px 0px -50px 0px",
    },
  );

  observer.observe(target.value);
});

onUnmounted(() => {
  observer?.disconnect();
});
</script>

<template>
  <div
    ref="target"
    class="reveal"
    :class="{ 'reveal--visible': isVisible }"
    :style="{
      '--reveal-translate-y': `${translateY}px`,
      '--reveal-duration': `${duration}ms`,
    }"
  >
    <slot />
  </div>
</template>

<style scoped>
.reveal {
  opacity: 0;
  transform: translateY(var(--reveal-translate-y, 20px));
  transition:
    opacity var(--reveal-duration, 700ms) ease-out,
    transform var(--reveal-duration, 700ms) ease-out;
  will-change: opacity, transform;
}

.reveal--visible {
  opacity: 1;
  transform: translateY(0);
}
</style>
