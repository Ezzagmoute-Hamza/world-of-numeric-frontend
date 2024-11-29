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
      </div>
      
      <div class="relative bg-white p-6 rounded-lg shadow">
        <h2 class="text-xl font-semibold mb-4 text-gray-700">Produits les plus vendus</h2>
        <ul class="space-y-2">
          <li v-for="product in topProducts" :key="product.id" class="flex justify-between">
            <span class="text-gray-600">{{ product.name }}</span>
            <span class="font-medium text-gray-800">{{ product.quantitySold }} unités</span>
          </li>
        </ul>
      </div>
      
      <div class="relative bg-white p-6 rounded-lg shadow">
        <h2 class="text-xl font-semibold mb-4 text-gray-700">Répartition des ventes par catégorie</h2>
        <ul class="space-y-2">
          <li v-for="(sales, category) in categorySales" :key="category" class="flex justify-between">
            <span class="text-gray-600">{{ category }}</span>
            <span class="font-medium text-gray-800">{{ 10 }} %</span>
          </li>
        </ul>
      </div>
    </div>
    
    <div class="relative bg-white p-6 rounded-lg shadow overflow-x-auto">
      <h2 class="text-xl font-semibold mb-4 text-gray-700">Liste des produits</h2>
      <table class="w-full">
        <thead>
          <tr class="text-left bg-gray-50">
            <th class="p-2">Nom du produit</th>
            <th class="p-2">Date d'ajout</th>
            <th class="p-2">Prix</th>
            <th class="p-2">Ventes totales</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="product in products" :key="product.id" class="border-t">
            <td class="p-2">{{ product.name }}</td>
            <td class="p-2">{{ product.dateAdded }}</td>
            <td class="p-2">{{ product.price }}</td>
            <td class="p-2">{{ product.quantitySold }}</td>
          </tr>
        </tbody>
      </table>
    </div>

  </div>
</template>

<script setup>
  
    import { ref, computed } from 'vue'

    const products = ref([
      {
        id: '1',
        name: 'Product A',
        price: 99.99,
        category: 'Electronics',
        dateAdded: '2024-01-15',
        totalSales: 4999.50,
        quantitySold: 50
      },
      {
        id: '2',
        name: 'Product B',
        price: 49.99,
        category: 'Clothing',
        dateAdded: '2024-01-20',
        totalSales: 2499.50,
        quantitySold: 50
      },
      {
        id: '3',
        name: 'Product C',
        price: 29.99,
        category: 'Books',
        dateAdded: '2024-01-25',
        totalSales: 899.70,
        quantitySold: 30
      },
      {
        id: '4',
        name: 'Product D',
        price: 199.99,
        category: 'Electronics',
        dateAdded: '2024-02-01',
        totalSales: 5999.70,
        quantitySold: 30
      },
      {
        id: '5',
        name: 'Product E',
        price: 15.99,
        category: 'Books',
        dateAdded: '2024-02-05',
        totalSales: 479.70,
        quantitySold: 30
      }
    ]);

    const currency = ' MAD';

    const timeRange = ref('7d');

    const handleTimeRange = computed(()=>{
      console.log(timeRange._value);
    });
    
    const topProducts = computed(() => {
      return [...products.value]
        .sort((a, b) => b.quantitySold - a.quantitySold)
        .slice(0, 5)
    });

    const categorySales = computed(() => {
      return products.value.reduce((acc, product) => {
        const sales = product.price * product.quantitySold
        acc[product.category] = (acc[product.category] || 0) + sales
        return acc
      }, {})
    });
    const calculatePercentage = (categoryTotal) => {
      const total = Object.values(categorySales.value).reduce((sum, sales) => sum + sales, 0)
      return ((categoryTotal / total) * 100).toFixed(1)
    }

    const totalSales = computed(() => {
      return products.value.reduce((sum, product) => sum + (product.price * product.quantitySold), 0)
    });

</script>