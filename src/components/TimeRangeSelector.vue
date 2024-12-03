<template>
  <div class="flex justify-between items-center">
    <h1 class="text-3xl font-bold text-gray-800">{{ title }}</h1>
    <select 
      v-model="selectedPeriod" 
      @change="handleTimeRangeChange" 
      class="border rounded p-2 bg-white"
    >
      <option value="7d">7 derniers jours</option>
      <option value="30d">30 derniers jours</option>
      <option value="12m">12 derniers mois</option>
    </select>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const props = defineProps({
  title: {
    type: String,
    default: 'Tableau de Bord',
  },
  onTimeRangeChange: {
    type: Function,
    required: true,
  },
});

const selectedPeriod = ref('7d');

const handleTimeRangeChange = () => {
  props.onTimeRangeChange(selectedPeriod.value);
};
</script>
