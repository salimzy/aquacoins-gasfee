<template>
  <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
    <!-- Hero Section -->
    <HeroSection 
      v-if="!isConnected"
      @connect="$emit('connect')"
    />
    
    <!-- Dashboard -->
    <Dashboard 
      v-else
      :active-tab="activeTab"
      :ton-balance="tonBalance"
      :aquacoin-balance="aquacoinBalance"
      :ton-price="tonPrice"
      :aquacoin-price="aquacoinPrice"
      :total-portfolio-value="totalPortfolioValue"
      :send-form="sendForm"
      :swap-form="swapForm"
      :transaction-history="transactionHistory"
      :is-transaction-pending="isTransactionPending"
      :is-swap-pending="isSwapPending"
      @tab-change="$emit('tab-change', $event)"
      @send-transaction="$emit('send-transaction')"
      @perform-swap="$emit('perform-swap')"
      @swap-tokens="$emit('swap-tokens')"
      @copy-address="$emit('copy-address')"
    />
  </div>
</template>

<script>
import HeroSection from './HeroSection.vue';
import Dashboard from './Dashboard.vue';

export default {
  components: {
    HeroSection,
    Dashboard
  },
  props: {
    // All props from parent
  }
};
</script>