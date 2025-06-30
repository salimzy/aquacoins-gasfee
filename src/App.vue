<!-- <template>
  <div id="app">
    <NavigationBar 
      :is-connected="isConnected"
      :wallet-address="walletAddress"
      :selected-wallet="selectedWallet"
      @connect="showWalletModal = true"
      @disconnect="disconnect"
    />
    
    <MainContent
      :is-connected="isConnected"
      :wallet-address="walletAddress"  
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
      @tab-change="activeTab = $event"
      @send-transaction="sendTransaction"
      @perform-swap="performSwap"
      @swap-tokens="swapTokens"
      @copy-address="copyAddress"
     
    />
    
    <WalletModal
      v-if="showWalletModal"
      :supported-wallets="supportedWallets"
      @close="showWalletModal = false"
      @connect="connectWallet"
    />
    
    <ConfirmationModal
      v-if="showConfirmationModal"
      :confirmation-data="confirmationData"
      @close="showConfirmationModal = false"
      @confirm="confirmTransaction"
    />
    
    <NotificationToast
      v-if="notification.show"
      :type="notification.type"
      :message="notification.message"
    />
  </div>
</template>

<script>
import NavigationBar from './components/NavigationBar.vue';
import MainContent from './components/MainContent.vue';
import WalletModal from './components/WalletModal.vue';
import ConfirmationModal from './components/ConfirmationModal.vue';
import NotificationToast from './components/NotificationToast.vue';

export default {
  name: 'App',
  components: {
    NavigationBar,
    MainContent,
    WalletModal,
    ConfirmationModal,
    NotificationToast
  },
  data() {
    return {
      isConnected: false,
      walletAddress: '',
      selectedWallet: '',
      showWalletModal: false,
      showConfirmationModal: false,
      activeTab: 'send',
      isTransactionPending: false,
      isSwapPending: false,
      tonBalance: '0.000000',
      aquacoinBalance: '0.000000',
      tonPrice: 2.45,
      aquacoinPrice: 0.12,
      sendForm: {
        token: 'TON',
        recipient: '',
        amount: '',
        message: ''
      },
      swapForm: {
        fromToken: 'TON',
        toToken: 'AQCNX',
        fromAmount: '',
        toAmount: ''
      },
      confirmationData: {
        amount: '',
        token: '',
        recipient: '',
        gasFee: '',
        total: ''
      },
      notification: {
        show: false,
        type: 'info',
        message: ''
      },
      supportedWallets: [
        {
          name: 'Tonkeeper',
          icon: 'fas fa-wallet',
          color: 'text-blue-600',
          description: 'Most popular TON wallet'
        }
      ],
      transactionHistory: []
    }
  },
  computed: {
    totalPortfolioValue() {
      const tonValue = parseFloat(this.tonBalance) * this.tonPrice;
      const aquaValue = parseFloat(this.aquacoinBalance) * this.aquacoinPrice;
      return tonValue + aquaValue;
    },
    estimatedGasFee() {
      return '0.005000';
    },
    swapRate() {
      if (this.swapForm.fromToken === 'TON' && this.swapForm.toToken === 'AQCNX') {
        return (this.tonPrice / this.aquacoinPrice).toFixed(6);
      } else if (this.swapForm.fromToken === 'AQCNX' && this.swapForm.toToken === 'TON') {
        return (this.aquacoinPrice / this.tonPrice).toFixed(6);
      }
      return '1.000000';
    }
  },
  watch: {
    'swapForm.fromAmount'(newAmount) {
      if (newAmount && !isNaN(parseFloat(newAmount))) {
        const rate = parseFloat(this.swapRate);
        this.swapForm.toAmount = (parseFloat(newAmount) * rate).toFixed(6);
      } else {
        this.swapForm.toAmount = '';
      }
    }
  },
  methods: {
    showNotification(type, message) {
      this.notification.type = type;
      this.notification.message = message;
      this.notification.show = true;
      setTimeout(() => {
        this.notification.show = false;
      }, 4000);
    },
    truncateAddress(address) {
      if (!address) return '';
      return `${address.slice(0, 6)}...${address.slice(-6)}`;
    },
    async connectWallet(walletName) {
      try {
        this.showWalletModal = false;
        this.showNotification('info', `Connecting to ${walletName}...`);
        
        await new Promise(resolve => setTimeout(resolve, 2000));
        
        this.isConnected = true;
        this.selectedWallet = walletName;
        this.walletAddress = 'EQD' + Math.random().toString(36).substring(2, 15) + Math.random().toString(36).substring(2, 15);
        
        this.tonBalance = (Math.random() * 10 + 1).toFixed(6);
        this.aquacoinBalance = (Math.random() * 1000 + 100).toFixed(6);
        
        this.showNotification('success', `Connected to ${walletName} successfully!`);
        this.activeTab = 'send';
      } catch (error) {
        this.showNotification('error', `Failed to connect to ${walletName}`);
      }
    },
    disconnect() {
      this.isConnected = false;
      this.walletAddress = '';
      this.selectedWallet = '';
      this.tonBalance = '0.000000';
      this.aquacoinBalance = '0.000000';
      this.showNotification('info', 'Wallet disconnected');
    },
    sendTransaction() {
      if (!this.sendForm.recipient || !this.sendForm.amount) {
        this.showNotification('error', 'Please fill in all required fields');
        return;
      }

      this.confirmationData.amount = this.sendForm.amount;
      this.confirmationData.token = this.sendForm.token;
      this.confirmationData.recipient = this.sendForm.recipient;
      this.confirmationData.gasFee = this.estimatedGasFee;
      this.confirmationData.total = (parseFloat(this.sendForm.amount) + parseFloat(this.estimatedGasFee)).toFixed(6);
      this.showConfirmationModal = true;
    },
    async confirmTransaction() {
      this.showConfirmationModal = false;
      this.isTransactionPending = true;
      
      try {
        await new Promise(resolve => setTimeout(resolve, 3000));
        
        if (this.sendForm.token === 'TON') {
          this.tonBalance = (parseFloat(this.tonBalance) - parseFloat(this.sendForm.amount)).toFixed(6);
        } else {
          this.aquacoinBalance = (parseFloat(this.aquacoinBalance) - parseFloat(this.sendForm.amount)).toFixed(6);
        }
        
        this.transactionHistory.unshift({
          hash: '0x' + Math.random().toString(36).substring(2, 15),
          type: 'send',
          amount: this.sendForm.amount,
          token: this.sendForm.token,
          timestamp: 'Just now',
          status: 'confirmed'
        });
        
        this.showNotification('success', 'Transaction successful!');
      } catch (error) {
        this.showNotification('error', 'Transaction failed');
      } finally {
        this.isTransactionPending = false;
        this.sendForm.amount = '';
        this.sendForm.recipient = '';
        this.sendForm.message = '';
      }
    },
    async performSwap() {
      if (!this.swapForm.fromAmount || isNaN(parseFloat(this.swapForm.fromAmount))) {
        this.showNotification('error', 'Please enter a valid amount');
        return;
      }

      this.isSwapPending = true;
      
      try {
        await new Promise(resolve => setTimeout(resolve, 3000));
        
        if (this.swapForm.fromToken === 'TON') {
          this.tonBalance = (parseFloat(this.tonBalance) - parseFloat(this.swapForm.fromAmount)).toFixed(6);
          this.aquacoinBalance = (parseFloat(this.aquacoinBalance) + parseFloat(this.swapForm.toAmount)).toFixed(6);
        } else {
          this.aquacoinBalance = (parseFloat(this.aquacoinBalance) - parseFloat(this.swapForm.fromAmount)).toFixed(6);
          this.tonBalance = (parseFloat(this.tonBalance) + parseFloat(this.swapForm.toAmount)).toFixed(6);
        }
        
        this.transactionHistory.unshift({
          hash: '0x' + Math.random().toString(36).substring(2, 15),
          type: 'swap',
          amount: `${this.swapForm.fromAmount} ${this.swapForm.fromToken} → ${this.swapForm.toAmount} ${this.swapForm.toToken}`,
          timestamp: 'Just now',
          status: 'confirmed'
        });
        
        this.showNotification('success', 'Swap completed successfully!');
      } catch (error) {
        this.showNotification('error', 'Swap failed');
      } finally {
        this.isSwapPending = false;
        this.swapForm.fromAmount = '';
        this.swapForm.toAmount = '';
      }
    },
    swapTokens() {
      const temp = this.swapForm.fromToken;
      this.swapForm.fromToken = this.swapForm.toToken;
      this.swapForm.toToken = temp;
      this.swapForm.fromAmount = this.swapForm.toAmount;
      this.swapForm.toAmount = '';
    },
    copyAddress() {
      navigator.clipboard.writeText(this.walletAddress);
      this.showNotification('success', 'Address copied to clipboard!');
    }
  }
};
</script> -->



   <style scoped>
        .gradient-bg {
            background: linear-gradient(135deg, #667eea 0%, #4407ec 100%);
        }
        .card-hover {
            transition: all 0.3s ease;
        }
        .card-hover:hover {
            transform: translateY(-4px);
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1);
        }
        .wallet-button {
            transition: all 0.2s ease;
        }
        .wallet-button:hover {
            transform: scale(1.02);
        }
        .pulse-animation {
            animation: pulse 2s infinite;
        }
        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.7; }
        }
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
    <template>
   

    <div id="app">
        <!-- Navigation -->
        <nav class="gradient-bg shadow-lg">
            <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                <div class="flex justify-between items-center h-16">
                    <div class="flex items-center">
                        <i class="fas fa-water text-white text-2xl mr-3"></i>
                        <span class="text-white text-xl font-bold">Aquacoinx</span>
                    </div>
                    <div class="flex items-center space-x-4">
                        <button v-if="!isConnected" @click="showWalletModal = true" 
                                class="bg-white text-blue-600 px-4 py-2 rounded-lg font-medium hover:bg-gray-100 transition">
                            <i class="fas fa-wallet mr-2"></i>Connect Wallet
                        </button>
                        <div v-else class="flex items-center space-x-3">
                            <div class="text-white text-sm">
                                <div class="font-medium">{{ truncateAddress(walletAddress) }}</div>
                                <div class="text-blue-200">{{ selectedWallet }}</div>
                            </div>
                            <button @click="disconnect" 
                                    class="bg-red-500 text-white px-3 py-2 rounded-lg hover:bg-red-600 transition">
                                <i class="fas fa-sign-out-alt"></i>
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </nav>

        <!-- Main Content -->
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
            <!-- Hero Section -->
            <div v-if="!isConnected" class="text-center py-16">
                <h1 class="text-5xl font-bold text-gray-900 mb-6">
                    Welcome to <span class="text-blue-600">Aquacoinx</span>
                </h1>
                <p class="text-xl text-gray-600 mb-8 max-w-2xl mx-auto">
                    The next-generation DeFi platform built on TON blockchain. 
                    Swap, stake, and manage your Aquacoin tokens with ease.
                </p>
                <button @click="showWalletModal = true" 
                        class="bg-blue-600 text-white px-8 py-4 rounded-xl text-lg font-medium hover:bg-blue-700 transition transform hover:scale-105">
                    <i class="fas fa-rocket mr-2"></i>Get Started
                </button>
            </div>

            <!-- Dashboard -->
            <div v-else>
                <!-- Balance Cards -->
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
                    <div class="bg-white p-6 rounded-xl shadow-lg card-hover">
                        <div class="flex items-center justify-between mb-4">
                            <h3 class="text-lg font-semibold text-gray-800">TON Balance</h3>
                            <i class="fas fa-coins text-blue-500 text-xl"></i>
                        </div>
                        <div class="text-3xl font-bold text-gray-900">{{ tonBalance }}</div>
                        <div class="text-sm text-gray-500 mt-2">≈ ${{ (parseFloat(tonBalance) * tonPrice).toFixed(2) }}</div>
                    </div>
                    
                    <div class="bg-white p-6 rounded-xl shadow-lg card-hover">
                        <div class="flex items-center justify-between mb-4">
                            <h3 class="text-lg font-semibold text-gray-800">Aquacoin Balance</h3>
                            <i class="fas fa-water text-blue-500 text-xl"></i>
                        </div>
                        <div class="text-3xl font-bold text-gray-900">{{ aquacoinBalance }}</div>
                        <div class="text-sm text-gray-500 mt-2">≈ ${{ (parseFloat(aquacoinBalance) * aquacoinPrice).toFixed(2) }}</div>
                    </div>
                    
                    <div class="bg-white p-6 rounded-xl shadow-lg card-hover">
                        <div class="flex items-center justify-between mb-4">
                            <h3 class="text-lg font-semibold text-gray-800">Total Portfolio</h3>
                            <i class="fas fa-chart-line text-green-500 text-xl"></i>
                        </div>
                        <div class="text-3xl font-bold text-gray-900">${{ totalPortfolioValue.toFixed(2) }}</div>
                        <div class="text-sm text-green-500 mt-2">+2.4% (24h)</div>
                    </div>
                </div>

                <!-- Action Buttons -->
                <div class="grid grid-cols-2 md:grid-cols-4 gap-4 mb-8">
                    <button @click="activeTab = 'send'" 
                            :class="['p-4 rounded-xl font-medium transition', activeTab === 'send' ? 'bg-blue-600 text-white' : 'bg-white text-gray-700 hover:bg-gray-50']">
                        <i class="fas fa-paper-plane mb-2 block text-xl"></i>Send
                    </button>
                    <button @click="activeTab = 'receive'" 
                            :class="['p-4 rounded-xl font-medium transition', activeTab === 'receive' ? 'bg-blue-600 text-white' : 'bg-white text-gray-700 hover:bg-gray-50']">
                        <i class="fas fa-qrcode mb-2 block text-xl"></i>Receive
                    </button>
                    <button @click="activeTab = 'swap'" 
                            :class="['p-4 rounded-xl font-medium transition', activeTab === 'swap' ? 'bg-blue-600 text-white' : 'bg-white text-gray-700 hover:bg-gray-50']">
                        <i class="fas fa-exchange-alt mb-2 block text-xl"></i>Swap
                    </button>
                    <button @click="activeTab = 'history'" 
                            :class="['p-4 rounded-xl font-medium transition', activeTab === 'history' ? 'bg-blue-600 text-white' : 'bg-white text-gray-700 hover:bg-gray-50']">
                        <i class="fas fa-history mb-2 block text-xl"></i>History
                    </button>
                </div>

                <!-- Tab Content -->
                <div class="bg-white rounded-xl shadow-lg p-6">
                    <!-- Send Tab -->
                    <div v-if="activeTab === 'send'">
                        <h3 class="text-xl font-semibold mb-6">Send Tokens</h3>
                        <form @submit.prevent="sendTransaction">
                            <div class="space-y-4">
                                <div>
                                    <label class="block text-sm font-medium text-gray-700 mb-2">Token</label>
                                    <select v-model="sendForm.token" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                                        <option value="TON">TON</option>
                                        <option value="AQUA">Aquacoin</option>
                                    </select>
                                </div>
                                <div>
                                    <label class="block text-sm font-medium text-gray-700 mb-2">Recipient Address</label>
                                    <input v-model="sendForm.recipient" type="text" placeholder="EQD..." 
                                           class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                                </div>
                                <div>
                                    <label class="block text-sm font-medium text-gray-700 mb-2">Amount</label>
                                    <input v-model="sendForm.amount" type="number" step="0.000001" placeholder="0.0" 
                                           class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                                </div>
                                <div>
                                    <label class="block text-sm font-medium text-gray-700 mb-2">Message (Optional)</label>
                                    <input v-model="sendForm.message" type="text" placeholder="Enter message..." 
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

                    <!-- Receive Tab -->
                    <div v-if="activeTab === 'receive'">
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
                            <button @click="copyAddress" class="bg-blue-600 text-white px-6 py-2 rounded-lg hover:bg-blue-700 transition">
                                <i class="fas fa-copy mr-2"></i>Copy Address
                            </button>
                        </div>
                    </div>

                    <!-- Swap Tab -->
                    <div v-if="activeTab === 'swap'">
                        <h3 class="text-xl font-semibold mb-6">Swap Tokens</h3>
                        <form @submit.prevent="performSwap">
                            <div class="space-y-4">
                                <div class="bg-gray-50 p-4 rounded-lg">
                                    <label class="block text-sm font-medium text-gray-700 mb-2">From</label>
                                    <div class="flex space-x-2">
                                        <select v-model="swapForm.fromToken" class="flex-1 p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500">
                                            <option value="TON">TON</option>
                                            <option value="AQUA">Aquacoin</option>
                                        </select>
                                        <input v-model="swapForm.fromAmount" type="number" step="0.000001" placeholder="0.0" 
                                               class="flex-2 p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500">
                                    </div>
                                </div>
                                
                                <div class="text-center">
                                    <button type="button" @click="swapTokens" class="bg-blue-100 text-blue-600 p-2 rounded-full hover:bg-blue-200 transition">
                                        <i class="fas fa-exchange-alt"></i>
                                    </button>
                                </div>
                                
                                <div class="bg-gray-50 p-4 rounded-lg">
                                    <label class="block text-sm font-medium text-gray-700 mb-2">To</label>
                                    <div class="flex space-x-2">
                                        <select v-model="swapForm.toToken" class="flex-1 p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500">
                                            <option value="TON">TON</option>
                                            <option value="AQUA">Aquacoin</option>
                                        </select>
                                        <input v-model="swapForm.toAmount" type="number" step="0.000001" placeholder="0.0" readonly
                                               class="flex-2 p-3 border border-gray-300 rounded-lg bg-gray-100">
                                    </div>
                                </div>
                                
                                <div class="bg-blue-50 p-4 rounded-lg">
                                    <div class="flex justify-between text-sm">
                                        <span>Exchange Rate:</span>
                                        <span>1 {{ swapForm.fromToken }} = {{ swapRate }} {{ swapForm.toToken }}</span>
                                    </div>
                                    <div class="flex justify-between text-sm mt-1">
                                        <span>Slippage Tolerance:</span>
                                        <span>0.5%</span>
                                    </div>
                                </div>
                                
                                <button type="submit" :disabled="isSwapPending" 
                                        class="w-full bg-blue-600 text-white py-3 rounded-lg font-medium hover:bg-blue-700 transition disabled:opacity-50">
                                    <span v-if="isSwapPending" class="flex items-center justify-center">
                                        <div class="loading-spinner mr-2"></div>
                                        Swapping...
                                    </span>
                                    <span v-else>Swap Tokens</span>
                                </button>
                            </div>
                        </form>
                    </div>

                    <!-- History Tab -->
                    <div v-if="activeTab === 'history'">
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
                </div>
            </div>
        </div>

        <!-- Wallet Connection Modal -->
        <div v-if="showWalletModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
            <div class="bg-white rounded-xl p-6 max-w-md w-full mx-4">
                <div class="flex justify-between items-center mb-6">
                    <h3 class="text-xl font-semibold">Connect Wallet</h3>
                    <button @click="showWalletModal = false" class="text-gray-400 hover:text-gray-600">
                        <i class="fas fa-times text-xl"></i>
                    </button>
                </div>
                <div class="space-y-3">
                    <button v-for="wallet in supportedWallets" :key="wallet.name"
                            @click="connectWallet(wallet.name)" 
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


        <!-- Confirmation Modal -->
        <div v-if="showConfirmationModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
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
                        <button @click="showConfirmationModal = false" 
                                class="flex-1 bg-gray-200 text-gray-800 py-2 rounded-lg hover:bg-gray-300 transition">
                            Cancel
                        </button>
                        <button @click="confirmTransaction" 
                                class="flex-1 bg-blue-600 text-white py-2 rounded-lg hover:bg-blue-700 transition">
                            Confirm
                        </button>
                    </div>
                </div>
            </div>
        </div>
        <!-- Notification Toast -->
        <div v-if="notification.show" :class="['fixed top-4 right-4 p-4 rounded-lg shadow-lg transition-all duration-300 z-50',
                                              notification.type === 'success' ? 'bg-green-500 text-white' : 
                                              notification.type === 'error' ? 'bg-red-500 text-white' : 
                                              'bg-blue-500 text-white']">
            <div class="flex items-center">
                <i :class="['fas mr-2', 
                           notification.type === 'success' ? 'fa-check-circle' : 
                           notification.type === 'error' ? 'fa-exclamation-circle' : 
                           'fa-info-circle']"></i>
                {{ notification.message }}
            </div>
        </div>
    </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      isConnected: false,
      walletAddress: '',
      selectedWallet: '',
      showWalletModal: false,
      showConfirmationModal: false,
      activeTab: 'send',
      isTransactionPending: false,
      isSwapPending: false,
      tonBalance: '0.000000',
      aquacoinBalance: '0.000000',
      tonPrice: 2.45,
      aquacoinPrice: 0.12,
      sendForm: {
        token: 'TON',
        recipient: '',
        amount: '',
        message: ''
      },
      swapForm: {
        fromToken: 'TON',
        toToken: 'AQUA',
        fromAmount: '',
        toAmount: ''
      },
      confirmationData: {
        amount: '',
        token: '',
        recipient: '',
        gasFee: '',
        total: ''
      },
      notification: {
        show: false,
        type: 'info',
        message: ''
      },
      supportedWallets: [
        {
          name: 'Tonkeeper',
          icon: 'fas fa-wallet',
          color: 'text-blue-600',
          description: 'Most popular TON wallet'
        },
        // ... other wallets
      ],
      transactionHistory: [
        // ... transaction history data
      ]
    }
  },
  computed: {
    totalPortfolioValue() {
      const tonValue = parseFloat(this.tonBalance) * this.tonPrice;
      const aquaValue = parseFloat(this.aquacoinBalance) * this.aquacoinPrice;
      return tonValue + aquaValue;
    },
    estimatedGasFee() {
      return '0.005000';
    },
    swapRate() {
      if (this.swapForm.fromToken === 'TON' && this.swapForm.toToken === 'AQUA') {
        return (this.tonPrice / this.aquacoinPrice).toFixed(6);
      } else if (this.swapForm.fromToken === 'AQUA' && this.swapForm.toToken === 'TON') {
        return (this.aquacoinPrice / this.tonPrice).toFixed(6);
      }
      return '1.000000';
    }
  },
  watch: {
    'swapForm.fromAmount'(newAmount) {
      if (newAmount && !isNaN(parseFloat(newAmount))) {
        const rate = parseFloat(this.swapRate);
        this.swapForm.toAmount = (parseFloat(newAmount) * rate).toFixed(6);
      } else {
        this.swapForm.toAmount = '';
      }
    }
  },
  methods: {
    showNotification(type, message) {
      this.notification.type = type;
      this.notification.message = message;
      this.notification.show = true;
      setTimeout(() => {
        this.notification.show = false;
      }, 4000);
    },
    truncateAddress(address) {
      if (!address) return '';
      return `${address.slice(0, 6)}...${address.slice(-6)}`;
    },
    async connectWallet(walletName) {
      try {
        this.showWalletModal = false;
        this.showNotification('info', `Connecting to ${walletName}...`);
        
        // Simulate wallet connection
        await new Promise(resolve => setTimeout(resolve, 2000));
        
        // Mock wallet connection
        this.isConnected = true;
        this.selectedWallet = walletName;
        this.walletAddress = 'EQD' + Math.random().toString(36).substring(2, 15) + Math.random().toString(36).substring(2, 15);
        
        // Load mock balances
        this.tonBalance = (Math.random() * 10 + 1).toFixed(6);
        this.aquacoinBalance = (Math.random() * 1000 + 100).toFixed(6);
        
        this.showNotification('success', `Connected to ${walletName} successfully!`);
        this.activeTab = 'send';
      } catch (error) {
        this.showNotification('error', `Failed to connect to ${walletName}`);
      }
    },
    disconnect() {
      this.isConnected = false;
      this.walletAddress = '';
      this.selectedWallet = '';
      this.tonBalance = '0.000000';
      this.aquacoinBalance = '0.000000';
      this.showNotification('info', 'Wallet disconnected');
    },
    sendTransaction() {
      if (!this.sendForm.recipient || !this.sendForm.amount) {
        this.showNotification('error', 'Please fill in all required fields');
        return;
      }

      this.confirmationData.amount = this.sendForm.amount;
      this.confirmationData.token = this.sendForm.token;
      this.confirmationData.recipient = this.sendForm.recipient;
      this.confirmationData.gasFee = this.estimatedGasFee;
      this.confirmationData.total = (parseFloat(this.sendForm.amount) + parseFloat(this.estimatedGasFee)).toFixed(6);
      this.showConfirmationModal = true;
    },
    async confirmTransaction() {
      this.showConfirmationModal = false;
      this.isTransactionPending = true;
      
      try {
        // Simulate transaction processing
        await new Promise(resolve => setTimeout(resolve, 3000));
        
        if (this.sendForm.token === 'TON') {
          this.tonBalance = (parseFloat(this.tonBalance) - parseFloat(this.sendForm.amount)).toFixed(6);
        } else {
          this.aquacoinBalance = (parseFloat(this.aquacoinBalance) - parseFloat(this.sendForm.amount)).toFixed(6);
        }
        
        this.transactionHistory.unshift({
          hash: '0x' + Math.random().toString(36).substring(2, 15),
          type: 'send',
          amount: this.sendForm.amount,
          token: this.sendForm.token,
          timestamp: 'Just now',
          status: 'confirmed'
        });
        
        this.showNotification('success', 'Transaction successful!');
      } catch (error) {
        this.showNotification('error', 'Transaction failed');
      } finally {
        this.isTransactionPending = false;
        this.sendForm.amount = '';
        this.sendForm.recipient = '';
        this.sendForm.message = '';
      }
    },
    async performSwap() {
      if (!this.swapForm.fromAmount || isNaN(parseFloat(this.swapForm.fromAmount))) {
        this.showNotification('error', 'Please enter a valid amount');
        return;
      }

      this.isSwapPending = true;
      
      try {
        // Simulate swap processing
        await new Promise(resolve => setTimeout(resolve, 3000));
        
        if (this.swapForm.fromToken === 'TON') {
          this.tonBalance = (parseFloat(this.tonBalance) - parseFloat(this.swapForm.fromAmount)).toFixed(6);
          this.aquacoinBalance = (parseFloat(this.aquacoinBalance) + parseFloat(this.swapForm.toAmount)).toFixed(6);
        } else {
          this.aquacoinBalance = (parseFloat(this.aquacoinBalance) - parseFloat(this.swapForm.fromAmount)).toFixed(6);
          this.tonBalance = (parseFloat(this.tonBalance) + parseFloat(this.swapForm.toAmount)).toFixed(6);
        }
        
        this.transactionHistory.unshift({
          hash: '0x' + Math.random().toString(36).substring(2, 15),
          type: 'swap',
          amount: `${this.swapForm.fromAmount} ${this.swapForm.fromToken} → ${this.swapForm.toAmount} ${this.swapForm.toToken}`,
          timestamp: 'Just now',
          status: 'confirmed'
        });
        
        this.showNotification('success', 'Swap completed successfully!');
      } catch (error) {
        this.showNotification('error', 'Swap failed');
      } finally {
        this.isSwapPending = false;
        this.swapForm.fromAmount = '';
        this.swapForm.toAmount = '';
      }
    },
    swapTokens() {
      const temp = this.swapForm.fromToken;
      this.swapForm.fromToken = this.swapForm.toToken;
      this.swapForm.toToken = temp;
      this.swapForm.fromAmount = this.swapForm.toAmount;
      this.swapForm.toAmount = '';
    },
    copyAddress() {
      navigator.clipboard.writeText(this.walletAddress);
      this.showNotification('success', 'Address copied to clipboard!');
    }
  }
}
</script>

<style>
/* All your CSS styles from the original file */
.gradient-bg {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}
/* ... rest of your styles ... */
</style>