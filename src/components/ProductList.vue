<template>
    <div class="relative bg-white p-6 rounded-lg shadow overflow-x-auto">
      <h2 class="text-xl font-semibold mb-4 text-gray-700">{{ title }}</h2>
      <table class="w-full">
        <thead>
          <tr class="text-left font-bold bg-gray-50 text-blue-700">
            <th class="p-2">Nom du produit</th>
            <th class="p-2">Prix<span>({{ currency }})</span></th>
            <th class="p-2">Ventes totales ({{ unit }})</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="product in paginatedProducts" :key="product.id" class="border-t">
            <td class="p-2 text-blue-600 font-medium">{{ product.productName }}</td>
            <td class="p-2">{{ product.price }}</td>
            <td class="p-2">{{ product.totalSales }}</td>
          </tr>
        </tbody>
      </table>
      <Loader v-if="isLoading" />
      <div class="mt-4 flex justify-between items-center">
        <div>
          <span class="text-sm text-gray-700">
            Affichage de {{ startIndex + 1 }} à {{ endIndex }} sur {{ products.length }} produits
          </span>
        </div>
        <div class="flex space-x-2">
          <button 
            @click="prevPage" 
            :disabled="currentPage === 1" 
            class="px-4 py-2 border rounded text-sm font-medium text-gray-700 bg-white hover:bg-gray-50 disabled:opacity-50"
            :aria-disabled="currentPage === 1"
          >
            Précédent
          </button>
          <button 
            @click="nextPage" 
            :disabled="currentPage === totalPages" 
            class="px-4 py-2 border rounded text-sm font-medium text-gray-700 bg-white hover:bg-gray-50 disabled:opacity-50"
            :aria-disabled="currentPage === totalPages"
          >
            Suivant
          </button>
        </div>
    </div>
    </div>
  </template>
  
  <script setup>
  import { computed, ref } from 'vue';
  import Loader from './Loader.vue';

  const props = defineProps({
    title: {
      type: String,
      default: 'Liste des produits',
    },
    products: {
      type: Array,
      required: true,
      default: () => [],
    },
    currency: {
      type: String,
      default: '',
    },
    unit: {
      type: String,
      default: 'unité',
    },
    isLoading: {
      type: Boolean,
      default: false,
    },
  });
  const productsList = ref(props.products || []);
  const currentPage = ref(1);
  const itemsPerPage = 10;
  const totalPages = computed(() => Math.ceil(productsList.value.length / itemsPerPage));
  const startIndex = computed(() => (currentPage.value - 1) * itemsPerPage)
  const endIndex = computed(() => Math.min(startIndex.value + itemsPerPage, productsList.value.length))

  const paginatedProducts = computed(() => {
    const start = (currentPage.value - 1) * itemsPerPage
    const end = start + itemsPerPage
    return productsList.value.slice(start, end)
  });
  const nextPage = () => {
    if (currentPage.value < totalPages.value) {
        currentPage.value++
    }
  }

  const prevPage = () => {
    if (currentPage.value > 1) {
        currentPage.value--
    }
  }
  
  </script>
  