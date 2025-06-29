<template>
  <div class="grid grid-cols-2 md:grid-cols-4 gap-4 mb-8">
    <button @click="$emit('tab-change', 'send')" 
            :class="['p-4 rounded-xl font-medium transition', activeTab === 'send' ? 'bg-blue-600 text-white' : 'bg-white text-gray-700 hover:bg-gray-50']">
      <i class="fas fa-paper-plane mb-2 block text-xl"></i>Send
    </button>
    <button @click="$emit('tab-change', 'receive')" 
            :class="['p-4 rounded-xl font-medium transition', activeTab === 'receive' ? 'bg-blue-600 text-white' : 'bg-white text-gray-700 hover:bg-gray-50']">
      <i class="fas fa-qrcode mb-2 block text-xl"></i>Receive
    </button>
    <button @click="$emit('tab-change', 'swap')" 
            :class="['p-4 rounded-xl font-medium transition', activeTab === 'swap' ? 'bg-blue-600 text-white' : 'bg-white text-gray-700 hover:bg-gray-50']">
      <i class="fas fa-exchange-alt mb-2 block text-xl"></i>Swap
    </button>
    <button @click="$emit('tab-change', 'history')" 
            :class="['p-4 rounded-xl font-medium transition', activeTab === 'history' ? 'bg-blue-600 text-white' : 'bg-white text-gray-700 hover:bg-gray-50']">
      <i class="fas fa-history mb-2 block text-xl"></i>History
    </button>
  </div>
</template>

<script>
export default {
  props: {
    activeTab: String
  }
};
</script>