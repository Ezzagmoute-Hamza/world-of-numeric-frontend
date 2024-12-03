<template>
  <div class="relative bg-white p-6 rounded-lg shadow">
    <h2 class="text-xl font-semibold mb-4 text-gray-700">{{ title }}</h2>
    <div class="space-y-2">
      <Pie v-if="!isError" :data="computedData" :options="options" />
      <img 
        v-if="isError" 
        src="../assets/internal_server_error.png" 
        alt="No data available" 
        class="w-full h-auto"
      />
    </div>
    <Loader v-if="isLoading" />
  </div>
</template>

<script setup>
import Loader from "./Loader.vue";
import { Chart as ChartJS, ArcElement, Tooltip, Legend } from 'chart.js'
import { computed } from "vue";
import { Pie } from 'vue-chartjs';

ChartJS.register(ArcElement, Tooltip, Legend)

const props = defineProps({
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

const computedData = computed(() => {
  return {
    labels: props.categorySales.map(category => category.category),
    datasets: [
      {
        backgroundColor: ["#FF5733","#33FF57","#3357FF","#F1C40F","#8E44AD","#1ABC9C","#E74C3C","#3498DB","#2ECC71","#F39C12"],
        data: props.categorySales.map(category => category.percentage.toFixed(2))
      }
    ]
  }
})

const options = {
  responsive: true,
  maintainAspectRatio: false
}
const isError = computed(() => !props.isLoading && props.categorySales.length === 0);

</script>
