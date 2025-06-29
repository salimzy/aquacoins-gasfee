<template>
  <div>
    <h3 class="text-xl font-semibold mb-6">Receive Tokens</h3>
    <div class="text-center">
      <div class="bg-gray-100 p-8 rounded-lg inline-block mb-4">
        <div class="w-48 h-48 bg-white border-2 border-dashed border-gray-300 rounded-lg flex items-center justify-center">
          <i class="fas fa-qrcode text-6xl text-gray-400"></i>
        </div>
      </div>
      <p class="text-lg font-medium mb-2">Your Wallet Address</p>
      <div class="bg-gray-100 p-3 rounded-lg mb-4">
        <code class="text-sm break-all">{{ walletAddress }}</code>
      </div>
      <button @click="$emit('copy-address')" class="bg-blue-600 text-white px-6 py-2 rounded-lg hover:bg-blue-700 transition">
        <i class="fas fa-copy mr-2"></i>Copy Address
      </button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    walletAddress: String
  }
};
</script>