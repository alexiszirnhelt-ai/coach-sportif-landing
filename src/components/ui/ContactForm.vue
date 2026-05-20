<script setup lang="ts">
import { ref, computed } from "vue";

// État des champs du formulaire
const name = ref("");
const email = ref("");
const message = ref("");

// État de soumission
type Status = "idle" | "loading" | "success" | "error";
const status = ref<Status>("idle");

// Validation simple côté client
const errors = ref<{ name?: string; email?: string; message?: string }>({});

function validate(): boolean {
  errors.value = {};

  if (!name.value.trim()) {
    errors.value.name = "Votre nom est requis.";
  }

  if (!email.value.trim()) {
    errors.value.email = "Votre email est requis.";
  } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email.value)) {
    errors.value.email = "Le format de l'email n'est pas valide.";
  }

  if (!message.value.trim()) {
    errors.value.message = "Un message est requis.";
  } else if (message.value.trim().length < 10) {
    errors.value.message = "Votre message doit faire au moins 10 caractères.";
  }

  return Object.keys(errors.value).length === 0;
}

const isLoading = computed(() => status.value === "loading");

async function handleSubmit() {
  if (!validate()) return;

  status.value = "loading";

  // Simulation d'un envoi API (1.2s)
  await new Promise((resolve) => setTimeout(resolve, 1200));

  // Simulation : on considère que ça réussit toujours
  status.value = "success";

  // Réinitialisation des champs après 100ms (le temps de voir le message)
  name.value = "";
  email.value = "";
  message.value = "";
}
</script>

<template>
  <form class="space-y-4" novalidate @submit.prevent="handleSubmit">
    <!-- Nom -->
    <div>
      <label for="name" class="sr-only">Nom complet</label>
      <input
        id="name"
        v-model="name"
        type="text"
        placeholder="Votre nom complet"
        autocomplete="name"
        :disabled="isLoading"
        class="w-full rounded-xl border border-neutral-700 bg-neutral-900 px-4 py-3 text-sm text-neutral-100 placeholder:text-neutral-500 focus:border-neutral-500 focus:outline-none focus:ring-2 focus:ring-neutral-700 disabled:opacity-60"
        :class="{ 'border-red-500/60': errors.name }"
      />
      <p v-if="errors.name" class="mt-1 text-xs text-red-400">
        {{ errors.name }}
      </p>
    </div>

    <!-- Email -->
    <div>
      <label for="email" class="sr-only">Email</label>
      <input
        id="email"
        v-model="email"
        type="email"
        placeholder="Votre email"
        autocomplete="email"
        :disabled="isLoading"
        class="w-full rounded-xl border border-neutral-700 bg-neutral-900 px-4 py-3 text-sm text-neutral-100 placeholder:text-neutral-500 focus:border-neutral-500 focus:outline-none focus:ring-2 focus:ring-neutral-700 disabled:opacity-60"
        :class="{ 'border-red-500/60': errors.email }"
      />
      <p v-if="errors.email" class="mt-1 text-xs text-red-400">
        {{ errors.email }}
      </p>
    </div>

    <!-- Message -->
    <div>
      <label for="message" class="sr-only">Message</label>
      <textarea
        id="message"
        v-model="message"
        rows="4"
        placeholder="Parlez-moi de vos objectifs..."
        :disabled="isLoading"
        class="w-full resize-none rounded-xl border border-neutral-700 bg-neutral-900 px-4 py-3 text-sm text-neutral-100 placeholder:text-neutral-500 focus:border-neutral-500 focus:outline-none focus:ring-2 focus:ring-neutral-700 disabled:opacity-60"
        :class="{ 'border-red-500/60': errors.message }"
      ></textarea>
      <p v-if="errors.message" class="mt-1 text-xs text-red-400">
        {{ errors.message }}
      </p>
    </div>

    <!-- Bouton de soumission -->
    <button
      type="submit"
      :disabled="isLoading"
      class="inline-flex w-full items-center justify-center gap-2 rounded-full bg-neutral-100 px-6 py-3 text-sm font-medium text-neutral-900 transition-colors hover:bg-white disabled:cursor-not-allowed disabled:opacity-70"
    >
      <span v-if="isLoading">Envoi en cours…</span>
      <span v-else>Réserver ma séance découverte</span>
    </button>

    <!-- Message de succès -->
    <transition
      enter-active-class="transition-all duration-300"
      leave-active-class="transition-all duration-200"
      enter-from-class="opacity-0 translate-y-2"
      enter-to-class="opacity-100 translate-y-0"
      leave-from-class="opacity-100 translate-y-0"
      leave-to-class="opacity-0 translate-y-2"
    >
      <div
        v-if="status === 'success'"
        class="rounded-xl border border-green-500/40 bg-green-500/10 px-4 py-3 text-sm text-green-300"
        role="status"
      >
        ✅ Message reçu ! Je vous recontacte sous 24h.
      </div>
    </transition>
  </form>
</template>
