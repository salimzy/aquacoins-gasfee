<template>
  <div>
    <!-- Balance Cards -->
    <BalanceCards 
      :ton-balance="tonBalance"
      :aquacoin-balance="aquacoinBalance"
      :ton-price="tonPrice"
      :aquacoin-price="aquacoinPrice"
      :total-portfolio-value="totalPortfolioValue"
    />
    
    <!-- Action Buttons -->
    <ActionButtons 
      :active-tab="activeTab"
      @tab-change="$emit('tab-change', $event)"
    />
    
    <!-- Tab Content -->
    <div class="bg-white rounded-xl shadow-lg p-6">
      <SendTab
        v-if="activeTab === 'send'"
        :send-form="sendForm"
        :is-transaction-pending="isTransactionPending"
        :estimated-gas-fee="estimatedGasFee"
        @send-transaction="$emit('send-transaction')"
        @update:send-form="$emit('update:send-form', $event)"
      />
      
      <ReceiveTab
        v-if="activeTab === 'receive'"
        :wallet-address="walletAddress"
        @copy-address="$emit('copy-address')"
      />
      
      <SwapTab
        v-if="activeTab === 'swap'"
        :swap-form="swapForm"
        :is-swap-pending="isSwapPending"
        :swap-rate="swapRate"
        @perform-swap="$emit('perform-swap')"
        @swap-tokens="$emit('swap-tokens')"
        @update:swap-form="$emit('update:swap-form', $event)"
      />
      
      <HistoryTab
        v-if="activeTab === 'history'"
        :transaction-history="transactionHistory"
      />
    </div>
  </div>
</template>

<script>
import BalanceCards from './BalanceCards.vue';
import ActionButtons from './ActionButtons.vue';
import SendTab from '../tabs/SendTab.vue';
import ReceiveTab from '../tabs/ReceiveTab.vue';
import SwapTab from '../tabs/SwapTab.vue';
import HistoryTab from '../tabs/HistoryTab.vue';

export default {
  components: {
    BalanceCards,
    ActionButtons,
    SendTab,
    ReceiveTab,
    SwapTab,
    HistoryTab
  },
  props: {
    activeTab: String,
    tonBalance: String,
    aquacoinBalance: String,
    tonPrice: Number,
    aquacoinPrice: Number,
    totalPortfolioValue: Number,
    sendForm: Object,
    swapForm: Object,
    transactionHistory: Array,
    isTransactionPending: Boolean,
    isSwapPending: Boolean,
    walletAddress: String,
    estimatedGasFee: String,
    swapRate: String
  }
};
</script>