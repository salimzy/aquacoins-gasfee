<template>
  <div>
    <h3 class="text-xl font-semibold mb-6">Transaction History</h3>
    <div class="space-y-3">
      <div v-for="tx in transactionHistory" :key="tx.hash" 
           class="flex items-center justify-between p-4 border border-gray-200 rounded-lg hover:bg-gray-50 transition">
        <div class="flex items-center space-x-3">
          <div :class="['w-10 h-10 rounded-full flex items-center justify-center', 
                       tx.type === 'send' ? 'bg-red-100 text-red-600' : 
                       tx.type === 'receive' ? 'bg-green-100 text-green-600' : 
                       'bg-blue-100 text-blue-600']">
            <i :class="['fas', 
                       tx.type === 'send' ? 'fa-arrow-up' : 
                       tx.type === 'receive' ? 'fa-arrow-down' : 
                       'fa-exchange-alt']"></i>
          </div>
          <div>
            <div class="font-medium">{{ tx.type === 'send' ? 'Sent' : tx.type === 'receive' ? 'Received' : 'Swapped' }}</div>
            <div class="text-sm text-gray-500">{{ tx.timestamp }}</div>
          </div>
        </div>
        <div class="text-right">
          <div class="font-medium">{{ tx.amount }} {{ tx.token }}</div>
          <div :class="['text-sm', tx.status === 'confirmed' ? 'text-green-600' : 'text-yellow-600']">
            {{ tx.status }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    transactionHistory: Array
  }
};
</script>