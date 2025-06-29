<template>
  <div>
    <h3 class="text-xl font-semibold mb-6">Send Tokens</h3>
    <form @submit.prevent="$emit('send-transaction')">
      <div class="space-y-4">
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-2">Token</label>
          <select v-model="sendForm.token" @change="$emit('update:send-form', sendForm)" 
                  class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
            <option value="TON">TON</option>
            <option value="AQUA">Aquacoin</option>
          </select>
        </div>
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-2">Recipient Address</label>
          <input v-model="sendForm.recipient" @input="$emit('update:send-form', sendForm)" type="text" placeholder="EQD..." 
                 class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
        </div>
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-2">Amount</label>
          <input v-model="sendForm.amount" @input="$emit('update:send-form', sendForm)" type="number" step="0.000001" placeholder="0.0" 
                 class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
        </div>
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-2">Message (Optional)</label>
          <input v-model="sendForm.message" @input="$emit('update:send-form', sendForm)" type="text" placeholder="Enter message..." 
                 class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
        </div>
        <div class="bg-gray-50 p-4 rounded-lg">
          <div class="flex justify-between items-center">
            <span class="text-sm text-gray-600">Estimated Gas Fee:</span>
            <span class="font-medium">{{ estimatedGasFee }} TON</span>
          </div>
        </div>
        <button type="submit" :disabled="isTransactionPending" 
                class="w-full bg-blue-600 text-white py-3 rounded-lg font-medium hover:bg-blue-700 transition disabled:opacity-50 disabled:cursor-not-allowed">
          <span v-if="isTransactionPending" class="flex items-center justify-center">
            <div class="loading-spinner mr-2"></div>
            Sending...
          </span>
          <span v-else>Send Transaction</span>
        </button>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  props: {
    sendForm: Object,
    isTransactionPending: Boolean,
    estimatedGasFee: String
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