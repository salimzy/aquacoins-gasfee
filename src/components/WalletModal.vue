<template>
  <div class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
    <div class="bg-white rounded-xl p-6 max-w-md w-full mx-4">
      <div class="flex justify-between items-center mb-6">
        <h3 class="text-xl font-semibold">Connect Wallet</h3>
        <button @click="$emit('close')" class="text-gray-400 hover:text-gray-600">
          <i class="fas fa-times text-xl"></i>
        </button>
      </div>
      <div class="space-y-3">
        <button v-for="wallet in supportedWallets" :key="wallet.name"
                @click="$emit('connect', wallet.name)" 
                class="wallet-button w-full flex items-center p-4 border border-gray-200 rounded-lg hover:border-blue-300 hover:bg-blue-50 transition">
          <i :class="wallet.icon + ' text-2xl mr-4 ' + wallet.color"></i>
          <div class="text-left">
            <div class="font-medium">{{ wallet.name }}</div>
            <div class="text-sm text-gray-500">{{ wallet.description }}</div>
          </div>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    supportedWallets: Array
  }
};
</script>

<style scoped>
.wallet-button {
  transition: all 0.2s ease;
}
.wallet-button:hover {
  transform: scale(1.02);
}
</style>