<template>
  <div class="text-center py-16">
    <h1 class="text-5xl font-bold text-gray-900 mb-6">
      Welcome to <span class="text-blue-600">Aquacoinx</span>
    </h1>
    <p class="text-xl text-gray-600 mb-8 max-w-2xl mx-auto">
      The next-generation DeFi platform built on TON blockchain. 
      Swap, stake, and manage your Aquacoin tokens with ease.
    </p>
    <button @click="handleConnectClick" @connect="console.log('Connect event received')"
            class="bg-blue-600 text-white px-8 py-4 rounded-xl text-lg font-medium hover:bg-blue-700 transition transform hover:scale-105">
      <i class="fas fa-rocket mr-2"></i>Get Started
    </button>
  </div>
</template>
<script>
export default {
  // No props needed for this simple component
    emits: ['connect'], // Declare emitted events
  methods: {
    handleConnectClick() {
      this.$emit('connect');
    }
  }
};
</script>