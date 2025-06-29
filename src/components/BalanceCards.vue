<template>
  <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
    <div class="bg-white p-6 rounded-xl shadow-lg card-hover">
      <div class="flex items-center justify-between mb-4">
        <h3 class="text-lg font-semibold text-gray-800">TON Balance</h3>
        <i class="fas fa-coins text-blue-500 text-xl"></i>
      </div>
      <div class="text-3xl font-bold text-gray-900">{{ tonBalance }}</div>
      <div class="text-sm text-gray-500 mt-2">≈ ${{ (parseFloat(tonBalance) * tonPrice).toFixed(2) }}</div>
    </div>
    
    <div class="bg-white p-6 rounded-xl shadow-lg card-hover">
      <div class="flex items-center justify-between mb-4">
        <h3 class="text-lg font-semibold text-gray-800">Aquacoin Balance</h3>
        <i class="fas fa-water text-blue-500 text-xl"></i>
      </div>
      <div class="text-3xl font-bold text-gray-900">{{ aquacoinBalance }}</div>
      <div class="text-sm text-gray-500 mt-2">≈ ${{ (parseFloat(aquacoinBalance) * aquacoinPrice).toFixed(2) }}</div>
    </div>
    
    <div class="bg-white p-6 rounded-xl shadow-lg card-hover">
      <div class="flex items-center justify-between mb-4">
        <h3 class="text-lg font-semibold text-gray-800">Total Portfolio</h3>
        <i class="fas fa-chart-line text-green-500 text-xl"></i>
      </div>
      <div class="text-3xl font-bold text-gray-900">${{ totalPortfolioValue.toFixed(2) }}</div>
      <div class="text-sm text-green-500 mt-2">+2.4% (24h)</div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    tonBalance: String,
    aquacoinBalance: String,
    tonPrice: Number,
    aquacoinPrice: Number,
    totalPortfolioValue: Number
  }
};
</script>

<style scoped>
.card-hover {
  transition: all 0.3s ease;
}
.card-hover:hover {
  transform: translateY(-4px);
  box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1);
}
</style>