<template>
  <div class="relative bg-white p-6 rounded-lg shadow">
    <h2 class="text-xl font-semibold mb-4 text-gray-700">{{ title }}</h2>
    <p class="text-3xl font-bold text-blue-600">
      {{ totalSales.toFixed(2) }}{{ currency }}
    </p>
    <Loader v-if="isLoading" />
  </div>
</template>

<script setup>
import Loader from "./Loader.vue";

defineProps({
  title: {
    type: String,
    default: "Ventes totales",
  },
  totalSales: {
    type: Number,
    required: true,
  },
  currency: {
    type: String,
    default: "$",
  },
  isLoading: {
    type: Boolean,
    default: false,
  },
});
</script>
