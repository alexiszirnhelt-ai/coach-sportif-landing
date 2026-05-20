<script setup lang="ts">
import { ref } from "vue";

interface Props {
  question: string;
  answer: string;
}

defineProps<Props>();

const isOpen = ref(false);

function toggle() {
  isOpen.value = !isOpen.value;
}
</script>

<template>
  <div
    class="overflow-hidden rounded-xl border border-neutral-800 bg-neutral-900/50"
  >
    <button
      type="button"
      class="flex w-full items-center justify-between gap-4 px-5 py-4 text-left transition-colors hover:bg-neutral-900"
      :aria-expanded="isOpen"
      @click="toggle"
    >
      <span class="text-sm font-medium text-neutral-100 md:text-base">
        {{ question }}
      </span>

      <svg
        xmlns="http://www.w3.org/2000/svg"
        class="h-5 w-5 shrink-0 text-neutral-400 transition-transform duration-300"
        :class="{ 'rotate-180': isOpen }"
        fill="none"
        viewBox="0 0 24 24"
        stroke="currentColor"
        stroke-width="2"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          d="m19.5 8.25-7.5 7.5-7.5-7.5"
        />
      </svg>
    </button>

    <transition
      enter-active-class="transition-all duration-300 ease-out"
      leave-active-class="transition-all duration-200 ease-in"
      enter-from-class="max-h-0 opacity-0"
      enter-to-class="max-h-96 opacity-100"
      leave-from-class="max-h-96 opacity-100"
      leave-to-class="max-h-0 opacity-0"
    >
      <div v-if="isOpen" class="overflow-hidden">
        <p class="px-5 pb-5 text-sm leading-relaxed text-neutral-300">
          {{ answer }}
        </p>
      </div>
    </transition>
  </div>
</template>
