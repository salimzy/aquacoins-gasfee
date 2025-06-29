<template>
  <div class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
    <div class="bg-white rounded-xl p-6 max-w-md w-full mx-4">
      <div class="text-center">
        <i class="fas fa-exclamation-triangle text-yellow-500 text-4xl mb-4"></i>
        <h3 class="text-xl font-semibold mb-4">Confirm Transaction</h3>
        <div class="space-y-2 text-left bg-gray-50 p-4 rounded-lg mb-6">
          <div class="flex justify-between">
            <span>Amount:</span>
            <span class="font-medium">{{ confirmationData.amount }} {{ confirmationData.token }}</span>
          </div>
          <div class="flex justify-between">
            <span>To:</span>
            <span class="font-medium">{{ truncateAddress(confirmationData.recipient) }}</span>
          </div>
          <div class="flex justify-between">
            <span>Gas Fee:</span>
            <span class="font-medium">{{ confirmationData.gasFee }} TON</span>
          </div>
          <div class="border-t pt-2 flex justify-between font-semibold">
            <span>Total:</span>
            <span>{{ confirmationData.total }} TON</span>
          </div>
        </div>
        <div class="flex space-x-3">
          <button @click="$emit('close')" 
                  class="flex-1 bg-gray-200 text-gray-800 py-2 rounded-lg hover:bg-gray-300 transition">
            Cancel
          </button>
          <button @click="$emit('confirm')" 
                  class="flex-1 bg-blue-600 text-white py-2 rounded-lg hover:bg-blue-700 transition">
            Confirm
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    confirmationData: Object
  },
  methods: {
    truncateAddress(address) {
      if (!address) return '';
      return `${address.slice(0, 6)}...${address.slice(-6)}`;
    }
  }
};
</script>