<template>
  <div class="min-h-screen bg-gray-100 p-6 space-y-6">
    <div class="flex justify-between items-center">
      <h1 class="text-3xl font-bold text-gray-800">Tableau de Bord</h1>
      <select v-model="timeRange" @change="handleTimeRange" class="border rounded p-2 bg-white">
        <option value="7d">7 derniers jours</option>
        <option value="30d">30 derniers jours</option>
        <option value="12m">12 derniers mois</option>
      </select>
    </div>
    
    <div class="grid gap-6 md:grid-cols-2 lg:grid-cols-3">
      <div class="relative bg-white p-6 rounded-lg shadow">
        <h2 class="text-xl font-semibold mb-4 text-gray-700">Ventes totales</h2>
        <p class="text-3xl font-bold text-blue-600">{{ totalSales.toFixed(2) }}{{ currency }}</p>
        <Loader v-if="apiServicesLoadingStatus.totalSales" />
      </div>
      
      <div class="relative bg-white p-6 rounded-lg shadow">
        <h2 class="text-xl font-semibold mb-4 text-gray-700">Produits les plus vendus</h2>
        <ul class="space-y-2">
          <li v-for="product in trendingProducts" :key="product.ProductName" class="flex justify-between">
            <span class="text-blue-600 font-bold">{{ product.ProductName }}</span>
            <span class="font-medium text-gray-800">{{ product.totalQuantity }} {{unit}}</span>
          </li>
        </ul>
        <Loader v-if="apiServicesLoadingStatus.trendingProducts"/>
      </div>
      
      <div class="relative bg-white p-6 rounded-lg shadow">
        <h2 class="text-xl font-semibold mb-4 text-gray-700">Répartition des ventes par catégorie</h2>
        <div class="space-y-2">
          <li v-for="category in categorySales" :key="category.category" class="grid grid-cols-12">
            <span class="text-blue-600 font-bold col-span-6">{{ category.category }}</span>
            <span class="font-medium text-gray-600 col-span-4">{{ category.salesCount }}</span>
            <span class="col-span-2 font-bold text-blue-600">{{ category.percentage.toFixed(2) }} %</span>
          </li>
        </div>
        <Loader v-if="apiServicesLoadingStatus.categorySales" />
      </div>

    </div>
    
    <div class="relative bg-white p-6 rounded-lg shadow overflow-x-auto">
      <h2 class="text-xl font-semibold mb-4 text-gray-700">Liste des produits</h2>
      <table class="w-full">
        <thead>
          <tr class="text-left font-bold  bg-gray-50 text-blue-700">
            <th class="p-2">Nom du produit</th>
            <th class="p-2">Prix<span>({{currency}})</span></th>
            <th class="p-2">Ventes totales ({{unit}})</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="product in products" :key="product.id" class="border-t">
            <td class="p-2 text-blue-600 font-medium">{{ product.productName }}</td>
            <td class="p-2">{{ product.price }}</td>
            <td class="p-2">{{ product.totalSales }}</td>
          </tr>
        </tbody>
      </table>
      <Loader v-if="apiServicesLoadingStatus.products" />
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, computed, onMounted } from 'vue';
import axios from 'axios';
import {useToast} from 'vue-toast-notification';
import 'vue-toast-notification/dist/theme-sugar.css';


import Loader from './components/Loader.vue';

const backendUrl = import.meta.env.VITE_BACKEND_SERVER_URL;
const products = ref([]);
const totalSales = ref(0);
const trendingProducts = ref([]);
const categorySales = ref([]);
const currency = ' DH';
const unit = ' unités';
const timeRange = ref('7d');

const $toast = useToast();

const displayToastError = (message)=>{
  $toast.open({
    message:`Failed to fetch ${message} Please try again later.`,
    type:"error",
    position:"top"
  });
}
const apiServicesLoadingStatus = reactive({
  products: false,
  totalSales: false,
  trendingProducts: false,
  categorySales: false,
});
const handleTimeRange = ()=>{
  fetchTotalSales(timeRange.value);
};
const fetchProducts = async () => {
  apiServicesLoadingStatus.products = true;
  try {
    const response = await axios.get(`${backendUrl}/products`);
    products.value = response.data.products;
  } catch (err) {
    displayToastError("products ");
    console.error(err);
  } finally {
    apiServicesLoadingStatus.products = false;
  }
};
const fetchTotalSales = async (period = timeRange.value) => {
  apiServicesLoadingStatus.totalSales = true;
  try {
    const response = await axios.get(`${backendUrl}/analytics/total_sales?period=${period}`);
    totalSales.value = response.data.totalSales;
  } catch (err) {
    displayToastError("Total sales");
    console.error(err);
  } finally {
    apiServicesLoadingStatus.totalSales = false;
  }
};
const fetchTrendingProducts = async () => {
  apiServicesLoadingStatus.trendingProducts = true;
  try {
    const response = await axios.get(`${backendUrl}/analytics/trending_products`);
    trendingProducts.value = response.data.trendingProducts;
  } catch (err) {
    displayToastError("Trending products");
    console.error(err);
  } finally {
    apiServicesLoadingStatus.trendingProducts = false;
  }
};
const fetchCategorySales = async () => {
  apiServicesLoadingStatus.categorySales = true;
  try {
    const response = await axios.get(`${backendUrl}/analytics/category_sales`);
    categorySales.value = response.data.categorySales;
  } catch (err) {
    displayToastError("Category sales");
    console.error(err);
  } finally {
    apiServicesLoadingStatus.categorySales = false;
  }
};

onMounted(() => {
  fetchCategorySales();
  fetchTotalSales();
  fetchTrendingProducts();
  fetchProducts();
});

</script>
