<template>
    <div class="wallet-connect">
        <button v-if="!connected" @click="connectWallet" :disabled="connecting"
            class="w-full px-3 py-2 bg-gray-700 hover:bg-gray-600 border border-gray-600 rounded-xl text-white font-medium transition-all duration-200 disabled:opacity-50 disabled:cursor-not-allowed flex items-center justify-center gap-3">
            <div v-if="connecting" class="w-4 h-4 border-2 border-white border-t-transparent rounded-full animate-spin"></div>
            <svg v-else class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" />
            </svg>
            {{ connecting ? 'Connecting...' : 'Connect Wallet' }}
        </button>
        
        <div v-else class="relative">
            <!-- Wallet Address Button -->
            <button @click="toggleDropdown" 
                class="flex items-center gap-3 px-3 py-2 bg-gray-700/50 hover:bg-gray-700 border border-gray-600 rounded-xl text-gray-300 font-medium transition-all duration-200">
                <div class="w-2 h-2 bg-green-400 rounded-full"></div>
                <span class="text-sm">{{ truncateAddress(wallet) }}</span>
                <svg :class="['w-4 h-4 transition-transform duration-200', showDropdown ? 'rotate-180' : '']" 
                    fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
                </svg>
            </button>

            <!-- Dropdown Menu -->
            <div v-if="showDropdown" 
                class="absolute right-0 mt-2 w-auto bg-gray-800 border border-gray-700 rounded-xl shadow-lg z-50">
                <div class="p-4">
                    <!-- Wallet Info -->
                    <div class="mb-4">
                        <div class="flex items-center gap-3 mb-2">
                            <div class="size-8 rounded-full bg-gradient-to-br from-blue-500 to-purple-600 flex items-center justify-center">
                                <span class="text-white text-xs font-bold">
                                    {{ wallet?.slice(0, 2).toUpperCase() }}
                                </span>
                            </div>
                            <div>
                                <p class="text-xs font-medium text-white">Connected Wallet</p>
                                <p class="text-xs text-gray-400">{{ truncateAddress(wallet) }}</p>
                            </div>
                        </div>
                    </div>

                    <!-- Copy Address -->
                    <button @click="copyAddress" 
                        class="w-full flex items-center text-left text-sm gap-3 px-3 py-2 text-gray-300 hover:bg-gray-700 rounded-lg transition-colors duration-200">
                        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z" />
                        </svg>
                        Copy Address
                    </button>

                    <!-- Disconnect -->
                    <button @click="disconnectWallet" :disabled="connecting"
                        class="w-full flex items-center text-left text-sm gap-3 px-3 py-2 text-red-400 hover:bg-red-600/20 rounded-lg transition-colors duration-200 disabled:opacity-50 disabled:cursor-not-allowed">
                        <div v-if="connecting" class="w-4 h-4 border-2 border-red-400 border-t-transparent rounded-full animate-spin"></div>
                        <svg v-else class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1" />
                        </svg>
                        {{ connecting ? 'Disconnecting...' : 'Disconnect' }}
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
declare global {
    interface Window {
        arweaveWallet: {
            connect: (permissions: string[]) => Promise<void>;
            disconnect: () => Promise<void>;
            getActiveAddress: () => Promise<string>;
        };
    }
}
const connected = ref(false);
const connecting = ref(false);
const wallet = ref<string | null>(null);
const showDropdown = ref(false);

const emit = defineEmits(['connect', 'disconnect']);

const truncateAddress = (addr: string | null) => {
    if (!addr) return '';
    return `${addr.slice(0, 6)}...${addr.slice(-4)}`;
};

const toggleDropdown = () => {
    showDropdown.value = !showDropdown.value;
};

const copyAddress = async () => {
    if (wallet.value) {
        try {
            await navigator.clipboard.writeText(wallet.value);
            // You could add a toast notification here
                    } catch (err) {
            console.error('Failed to copy address:', err);
        }
    }
    showDropdown.value = false;
};
const connectWallet = async () => {
    if (import.meta.client && window.arweaveWallet) {
        connecting.value = true;
        try {
            await window.arweaveWallet.connect(['ACCESS_ADDRESS', 'SIGN_TRANSACTION']);
            const address = await window.arweaveWallet.getActiveAddress();
            wallet.value = address;
            connected.value = true;
            emit('connect', address);
        } catch (error) {
            console.error('Failed to connect wallet:', error);
        } finally {
            connecting.value = false;
        }
    } else {
        alert('Please install ArConnect or another Arweave wallet extension');
    }
};
const disconnectWallet = async () => {
    try {
        connecting.value = true;
        await window.arweaveWallet.disconnect();
        wallet.value = null;
        connected.value = false;
        showDropdown.value = false;
        emit('disconnect');
        // toast({
        //     title: "Wallet Disconnected",
        //     description: "Successfully disconnected from ArConnect",
        // });
        connecting.value = false;
    } catch (error: any) {
        connecting.value = false;
        // toast({
        //     title: "Disconnect Failed",
        //     description: "Failed to disconnect wallet",
        //     variant: "destructive",
        // });
            }
};
const checkConnection = async () => {
    try {
        const addr = await window.arweaveWallet.getActiveAddress();
        wallet.value = addr;
        connected.value = true;
        emit('connect', addr);
    } catch (error: any) {
        wallet.value = null;
        connected.value = false;
            }
};
// Close dropdown when clicking outside
const handleClickOutside = (event: MouseEvent) => {
    const target = event.target as HTMLElement;
    if (!target.closest('.wallet-connect')) {
        showDropdown.value = false;
    }
};

onMounted(() => {
    document.addEventListener('click', handleClickOutside);
});

onUnmounted(() => {
    document.removeEventListener('click', handleClickOutside);
});

watchEffect(() => {
    checkConnection()
})
</script>