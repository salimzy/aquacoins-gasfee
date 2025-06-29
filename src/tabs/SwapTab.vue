<template>
  <div>
    <h3 class="text-xl font-semibold mb-6">Swap Tokens</h3>
    <form @submit.prevent="$emit('perform-swap')">
      <div class="space-y-4">
        <div class="bg-gray-50 p-4 rounded-lg">
          <label class="block text-sm font-medium text-gray-700 mb-2">From</label>
          <div class="flex space-x-2">
            <select v-model="swapForm.fromToken" @change="$emit('update:swap-form', swapForm)" 
                    class="flex-1 p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500">
              <option value="TON">TON</option>
              <option value="AQUA">Aquacoin</option>
            </select>
            <input v-model="swapForm.fromAmount" @input="$emit('update:swap-form', swapForm)" type="number" step="0.000001" placeholder="0.0" 
                   class="flex-2 p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500">
          </div>
        </div>
        
        <div class="text-center">
          <button type="button" @click="$emit('swap-tokens')" class="bg-blue-100 text-blue-600 p-2 rounded-full hover:bg-blue-200 transition">
            <i class="fas fa-exchange-alt"></i>
          </button>
        </div>
        
        <div class="bg-gray-50 p-4 rounded-lg">
          <label class="block text-sm font-medium text-gray-700 mb-2">To</label>
          <div class="flex space-x-2">
            <select v-model="swapForm.toToken" @change="$emit('update:swap-form', swapForm)" 
                    class="flex-1 p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500">
              <option value="TON">TON</option>
              <option value="AQUA">Aquacoin</option>
            </select>
            <input v-model="swapForm.toAmount" type="number" step="0.000001" placeholder="0.0" readonly
                   class="flex-2 p-3 border border-gray-300 rounded-lg bg-gray-100">
          </div>
        </div>
        
        <div class="bg-blue-50 p-4 rounded-lg">
          <div class="flex justify-between text-sm">
            <span>Exchange Rate:</span>
            <span>1 {{ swapForm.fromToken }} = {{ swapRate }} {{ swapForm.toToken }}</span>
          </div>
          <div class="flex justify-between text-sm mt-1">
            <span>Slippage Tolerance:</span>
            <span>0.5%</span>
          </div>
        </div>
        
        <button type="submit" :disabled="isSwapPending" 
                class="w-full bg-blue-600 text-white py-3 rounded-lg font-medium hover:bg-blue-700 transition disabled:opacity-50">
          <span v-if="isSwapPending" class="flex items-center justify-center">
            <div class="loading-spinner mr-2"></div>
            Swapping...
          </span>
          <span v-else>Swap Tokens</span>
        </button>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  props: {
    swapForm: Object,
    isSwapPending: Boolean,
    swapRate: String
  }
};
</script>

<style scoped>
.loading-spinner {
  border: 2px solid #f3f3f3;
  border-top: 2px solid #3498db;
  border-radius: 50%;
  width: 20px;
  height: 20px;
  animation: spin 1s linear infinite;
}
@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style>