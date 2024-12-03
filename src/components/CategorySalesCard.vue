<template>
  <div class="relative bg-white p-6 rounded-lg shadow">
    <h2 class="text-xl font-semibold mb-4 text-gray-700">{{ title }}</h2>
    <div class="space-y-2">
      <ul>
        <li
          v-for="category in categorySales"
          :key="category.category"
          class="grid grid-cols-12"
        >
          <span class="text-blue-600 font-bold col-span-6">
            {{ category.category }}
          </span>
          <span class="font-medium text-gray-600 col-span-4">
            {{ category.salesCount }}
          </span>
          <span class="col-span-2 font-bold text-blue-600">
            {{ category.percentage.toFixed(2) }} %
          </span>
        </li>
      </ul>
    </div>
    <Loader v-if="isLoading" />
  </div>
</template>

<script setup>
import Loader from "./Loader.vue";

defineProps({
  title: {
    type: String,
    default: "Répartition des ventes par catégorie",
  },
  categorySales: {
    type: Array,
    required: true,
    default: () => [],
  },
  isLoading: {
    type: Boolean,
    default: false,
  },
});
</script>
