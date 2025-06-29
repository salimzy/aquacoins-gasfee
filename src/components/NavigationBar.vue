<template>
  <nav class="gradient-bg shadow-lg">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex justify-between items-center h-16">
        <div class="flex items-center">
          <i class="fas fa-water text-white text-2xl mr-3"></i>
          <span class="text-white text-xl font-bold">Aquacoinx</span>
        </div>
        <div class="flex items-center space-x-4">
          <button v-if="!isConnected" @click="$emit('connect')" 
                  class="bg-white text-blue-600 px-4 py-2 rounded-lg font-medium hover:bg-gray-100 transition">
            <i class="fas fa-wallet mr-2"></i>Connect Wallet
          </button>
          <div v-else class="flex items-center space-x-3">
            <div class="text-white text-sm">
              <div class="font-medium">{{ truncateAddress(walletAddress) }}</div>
              <div class="text-blue-200">{{ selectedWallet }}</div>
            </div>
            <button @click="$emit('disconnect')" 
                    class="bg-red-500 text-white px-3 py-2 rounded-lg hover:bg-red-600 transition">
              <i class="fas fa-sign-out-alt"></i>
            </button>
          </div>
        </div>
      </div>
    </div>
  </nav>
</template>

<script>
export default {
  props: {
    isConnected: Boolean,
    walletAddress: String,
    selectedWallet: String
  },
  methods: {
    truncateAddress(address) {
      if (!address) return '';
      return `${address.slice(0, 6)}...${address.slice(-6)}`;
    }
  }
};
</script>

<style scoped>
.gradient-bg {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}
</style>