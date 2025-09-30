<template>
  <div class="min-h-screen bg-gray-900">
    <!-- Navbar -->
    <Navbar :handleWalletConnect="handleWalletConnect" :handleWalletDisconnect="handleWalletDisconnect" />

    <div class="container mx-auto px-4 py-6 sm:py-8 max-w-7xl mt-14 sm:mt-16">
      <div class="max-w-6xl mx-auto">

        <!-- Process ID Input -->
        <div v-if="wallet" class="bg-gray-800/50 backdrop-blur-sm p-4 sm:p-6 rounded-2xl border border-gray-700/50 mb-8">
          <div class="flex items-center gap-3 mb-6">
            <div class="w-8 h-8 bg-purple-500/20 rounded-lg flex items-center justify-center">
              <svg class="w-5 h-5 text-purple-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" />
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
              </svg>
            </div>
            <h2 class="text-lg sm:text-xl font-semibold text-white">Process Configuration</h2>
          </div>
          <div class="space-y-4">
            <div>
              <label for="processId" class="block text-sm font-medium text-gray-300 mb-2">
                Process ID
              </label>
              <input type="text" id="processId" v-model="processId"
                class="w-full px-4 py-3 bg-gray-700/50 border border-gray-600 rounded-xl focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all duration-200 text-white placeholder-gray-400"
                placeholder="Enter your AO process ID" />
              <p class="text-sm text-gray-400 mt-2">
                Deploy the TaskMaster process and enter the process ID here
              </p>
            </div>
          </div>
        </div>

        <!-- Main Content -->
        <div v-if="aoClient && processId">
          <!-- Tabs -->
          <div class="mb-8">
            <div class="border-b border-gray-700/50">
              <nav class="-mb-px flex space-x-8">
                <button
                  @click="activeTab = 'home'"
                  :class="[
                    'py-4 px-1 border-b-2 font-medium text-sm transition-colors duration-200',
                    activeTab === 'home'
                      ? 'border-blue-500 text-blue-400'
                      : 'border-transparent text-gray-400 hover:text-gray-300 hover:border-gray-300'
                  ]"
                >
                  <div class="flex items-center gap-2">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 7v10a2 2 0 002 2h14a2 2 0 002-2V9a2 2 0 00-2-2H5a2 2 0 00-2-2z" />
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 5a2 2 0 012-2h4a2 2 0 012 2v6H8V5z" />
                    </svg>
                    Home
                  </div>
                </button>
                <button
                  @click="activeTab = 'leaderboard'"
                  :class="[
                    'py-4 px-1 border-b-2 font-medium text-sm transition-colors duration-200',
                    activeTab === 'leaderboard'
                      ? 'border-blue-500 text-blue-400'
                      : 'border-transparent text-gray-400 hover:text-gray-300 hover:border-gray-300'
                  ]"
                >
                  <div class="flex items-center gap-2">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z" />
                    </svg>
                    Leaderboard
                  </div>
                </button>
              </nav>
            </div>
          </div>

          <!-- Tab Content -->
          <div v-show="activeTab === 'home'" class="grid grid-cols-1 lg:grid-cols-3 gap-8">
            <!-- Left Column - Create Task & Points -->
            <div class="lg:col-span-1 space-y-6">
              <PointsDisplay ref="pointsRef" :aoClient="aoClient" />
              <CreateTask :aoClient="aoClient" @task-created="handleTaskCreated" />
            </div>

            <!-- Right Column - Task List -->
            <div class="lg:col-span-2">
              <div class="bg-gray-800/50 backdrop-blur-sm p-4 sm:p-6 rounded-2xl border border-gray-700/50">
                <TaskList ref="taskListRef" :aoClient="aoClient" @refresh="handleRefresh" />
              </div>
            </div>
          </div>

          <!-- Leaderboard Tab -->
          <div v-show="activeTab === 'leaderboard'">
            <Leaderboard :aoClient="aoClient" />
          </div>
        </div>

        <!-- Instructions -->
        <div v-if="(!aoClient || !processId) && wallet" class="bg-gray-800/30 border border-gray-700/50 rounded-2xl p-6">
          <div class="flex items-center gap-3 mb-6">
            <div class="w-8 h-8 bg-blue-500/20 rounded-lg flex items-center justify-center">
              <svg class="w-5 h-5 text-blue-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
              </svg>
            </div>
            <h3 class="text-xl font-semibold text-white">Getting Started</h3>
          </div>
          <ol class="list-decimal list-inside space-y-2 text-gray-300">
            <li>Deploy the TaskMaster AO process using the aos CLI</li>
            <li>Enter the process ID in the field above</li>
            <li>Start creating and managing your tasks!</li>
          </ol>
          <div class="mt-6 p-4 bg-gray-700/50 rounded-xl border border-gray-600/50">
            <p class="text-gray-300">
              <strong>Quick Deploy:</strong> Run <code class="bg-gray-600 px-2 py-1 rounded font-mono text-sm text-blue-300">aos taskmaster</code>
              then <code class="bg-gray-600 px-2 py-1 rounded font-mono text-sm text-blue-300">.load ao/task-process.lua</code>
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { AOClient } from '~/lib/aoconnect';

useHead({
  title: 'TaskMaster - AO Task Management',
  meta: [
    { name: 'description', content: 'Decentralized task management powered by AO and Arweave' },
  ],
});

const wallet = ref<string | null>(null);
const aoClient = ref<AOClient | null>(null);
const processId = ref('');
const activeTab = ref('home');

const handleWalletConnect = (connectedWallet: string) => {

    wallet.value = connectedWallet;
};

const handleWalletDisconnect = () => {
  wallet.value = null;
  aoClient.value = null;
};

const taskListRef = ref();
const pointsRef = ref();

const handleTaskCreated = () => {
  // Refresh the task list after creating a task
  if (taskListRef.value) {
    taskListRef.value.fetchTasks();
  }
  // Also refresh points
  if (pointsRef.value) {
    pointsRef.value.fetchPoints();
  }
};

const handleRefresh = () => {
  // Handle refresh events from child components (e.g., when task is completed)
  if (pointsRef.value) {
    pointsRef.value.fetchPoints();
  }
};

watch([wallet, processId], () => {
  if (wallet.value && processId.value) {
      aoClient.value = new AOClient(processId.value);
  }
});
</script>

<style>
body {
  background-color: #101828;
}
</style>