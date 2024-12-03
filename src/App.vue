<template>
  <div class="min-h-screen bg-gray-100 p-6 space-y-6">
    <TimeRangeSelector 
      :title="'Tableau de Bord'"
      :onTimeRangeChange="handleTimeRange" 
    />
    <div class="grid gap-6 md:grid-cols-2 lg:grid-cols-3">
        <TotalSalesCard
          :title="'Ventes totales'"
          :totalSales="totalSales"
          :currency="currency"
          :isLoading="apiServicesLoadingStatus.totalSales"
        />
        <TrendingProductsCard
          :title="'Produits les plus vendus'"
          :products="trendingProducts"
          :unit="unit"
          :isLoading="apiServicesLoadingStatus.trendingProducts"
        />
        <CategorySalesCard
          :title="'Répartition des ventes par catégorie'"
          :categorySales="categorySales"
          :isLoading="apiServicesLoadingStatus.categorySales"
        />
    </div>
    <ProductList 
      :products="products" 
      :currency="currency" 
      :unit="unit" 
      :isLoading="apiServicesLoadingStatus.products" 
    />
  </div>
</template>

<script setup>
import { ref, reactive, onMounted } from 'vue';
import axios from 'axios';
import {useToast} from 'vue-toast-notification';
import 'vue-toast-notification/dist/theme-sugar.css';

import TotalSalesCard from "./components/TotalSalesCard.vue";
import TrendingProductsCard from "./components/TrendingProductsCard.vue"; 
import CategorySalesCard from "./components/CategorySalesCard.vue"; 
import TimeRangeSelector from "./components/TimeRangeSelector.vue"; 
import ProductList from "./components/ProductList.vue"; 

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
const handleTimeRange = (value)=>{
  timeRange.value = value;
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
