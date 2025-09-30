<template>
  <nav class="fixed top-0 left-0 right-0 z-50 bg-gray-800/50 backdrop-blur-sm border-b border-gray-700/50">
    <div class="container mx-auto px-4 py-3 sm:py-4 max-w-7xl">
      <div class="flex items-center justify-between">
        <!-- Logo and Title -->
        <div class="flex items-center gap-2 sm:gap-3 min-w-0 flex-1">
          <div class="w-8 h-8 sm:w-10 sm:h-10 bg-white/10 rounded-xl flex items-center justify-center backdrop-blur-sm border border-white/20 flex-shrink-0">
            <span class="text-lg sm:text-xl">⚡</span>
          </div>
          <div class="hidden sm:block min-w-0 flex-1">
            <h1 class="text-lg sm:text-xl font-bold text-white truncate">
              TaskMaster
            </h1>
            <p class="text-xs text-gray-400 hidden sm:block">
              Decentralized task management
            </p>
          </div>
        </div>
        
        <!-- Wallet Connect -->
        <div class="flex items-center flex-shrink-0 ml-2">
          <WalletConnect @connect="handleWalletConnect" @disconnect="handleWalletDisconnect" />
        </div>
      </div>
    </div>
  </nav>
</template>

<script setup lang="ts">
interface Props {
  handleWalletConnect: (connectedWallet: string) => void;
  handleWalletDisconnect: () => void;
}

defineProps<Props>();

</script>