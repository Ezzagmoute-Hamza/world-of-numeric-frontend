<template>
  <div class="relative bg-white p-6 rounded-lg shadow">
    <h2 class="text-xl font-semibold mb-4 text-gray-700">{{ title }}</h2>
    <ul class="space-y-2">
      <li
        v-for="product in products"
        :key="product.ProductName"
        class="flex justify-between"
      >
        <span class="text-blue-600 font-bold">{{ product.ProductName }}</span>
        <span class="font-medium text-gray-800">
          {{ product.totalQuantity }} {{ unit }}
        </span>
      </li>
    </ul>
    <Loader v-if="isLoading" />
  </div>
</template>

<script setup>
import Loader from "./Loader.vue";

defineProps({
  title: {
    type: String,
    default: "Produits les plus vendus",
  },
  products: {
    type: Array,
    required: true,
    default: () => [],
  },
  unit: {
    type: String,
    default: "",
  },
  isLoading: {
    type: Boolean,
    default: false,
  },
});
</script>
