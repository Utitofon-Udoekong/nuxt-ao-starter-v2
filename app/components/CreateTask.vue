<template>
    <div class="bg-gray-800/50 border border-gray-700/50 p-4 sm:p-6 rounded-2xl">
      <div class="flex items-center gap-3 mb-6">
        <div class="w-8 h-8 bg-blue-500/20 rounded-lg flex items-center justify-center">
          <svg class="w-5 h-5 text-blue-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6" />
          </svg>
        </div>
        <h2 class="text-lg font-semibold text-white">
          Create Task
        </h2>
      </div>
      
      <form @submit.prevent="handleSubmit" class="space-y-4">
        <div class="space-y-2">
          <label for="title" class="block text-sm font-medium text-gray-300">
            Task Title *
          </label>
          <input
            id="title"
            v-model="form.title"
            type="text"
            required
            class="w-full px-4 py-3 bg-gray-700/50 border border-gray-600 rounded-xl focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all duration-200 text-white placeholder-gray-400"
            placeholder="What needs to be done?"
          />
        </div>
        
        <div class="space-y-2">
          <label for="description" class="block text-sm font-medium text-gray-300">
            Description
          </label>
          <textarea
            id="description"
            v-model="form.description"
            rows="3"
            class="w-full px-4 py-3 bg-gray-700/50 border border-gray-600 rounded-xl focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all duration-200 text-white placeholder-gray-400 resize-none"
            placeholder="Add more details about this task..."
          ></textarea>
        </div>
        
        <div class="space-y-2">
          <label for="priority" class="block text-sm font-medium text-gray-300">
            Priority Level
          </label>
          <div class="grid grid-cols-3 gap-2">
            <button
              v-for="p in ['low', 'medium', 'high']"
              :key="p"
              type="button"
              @click="form.priority = p as 'low' | 'medium' | 'high'"
              :class="[
                'p-3 rounded-xl border transition-all duration-200',
                form.priority === p 
                  ? getPriorityConfig(p).bg + ' ' + getPriorityConfig(p).border
                  : 'border-gray-600 bg-gray-700/50 hover:bg-gray-700'
              ]"
            >
              <div class="flex flex-col items-center gap-1">
                <span class="text-sm">{{ getPriorityConfig(p).icon }}</span>
                <span :class="[
                  'text-xs font-medium',
                  form.priority === p ? getPriorityConfig(p).color : 'text-gray-400'
                ]">
                  {{ p.charAt(0).toUpperCase() + p.slice(1) }}
                </span>
              </div>
            </button>
          </div>
        </div>
        
        <button
          type="submit"
          :disabled="isSubmitting || !form.title.trim()"
          class="w-full bg-blue-600 hover:bg-blue-700 text-white font-medium py-3 px-6 rounded-xl transition-all duration-200 disabled:opacity-50 disabled:cursor-not-allowed flex items-center justify-center gap-3"
        >
          <div v-if="isSubmitting" class="w-4 h-4 border-2 border-white border-t-transparent rounded-full animate-spin"></div>
          <svg v-else class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6" />
          </svg>
          {{ isSubmitting ? 'Creating...' : 'Create Task' }}
        </button>
      </form>
    </div>
  </template>
  
  <script setup lang="ts">
  import { AOClient } from '~/lib/aoconnect';
  
  interface Props {
    aoClient: AOClient;
  }
  
  const props = defineProps<Props>();
  const emit = defineEmits<{
    taskCreated: [];
  }>();
  
  const form = ref({
    title: '',
    description: '',
    priority: 'medium' as 'low' | 'medium' | 'high'
  });
  
  const isSubmitting = ref(false);
  
  const getPriorityConfig = (priority: string) => {
    switch (priority) {
      case 'high': 
        return { 
          color: 'text-red-400', 
          bg: 'bg-red-500/20', 
          border: 'border-red-500/50',
          icon: '🔴',
          gradient: 'from-red-500 to-pink-500'
        };
      case 'medium': 
        return { 
          color: 'text-yellow-400', 
          bg: 'bg-yellow-500/20', 
          border: 'border-yellow-500/50',
          icon: '🟡',
          gradient: 'from-yellow-500 to-orange-500'
        };
      case 'low': 
        return { 
          color: 'text-green-400', 
          bg: 'bg-green-500/20', 
          border: 'border-green-500/50',
          icon: '🟢',
          gradient: 'from-green-500 to-emerald-500'
        };
      default: 
        return { 
          color: 'text-gray-400', 
          bg: 'bg-gray-500/20', 
          border: 'border-gray-500/50',
          icon: '⚪',
          gradient: 'from-gray-500 to-slate-500'
        };
    }
  };
  
  const handleSubmit = async () => {
    if (!props.aoClient) return;
    
    isSubmitting.value = true;
    try {
      const result = await props.aoClient.createTask({
        title: form.value.title,
        description: form.value.description,
        priority: form.value.priority
      });
      
      if (result.success) {
        // Reset form
        form.value = {
          title: '',
          description: '',
          priority: 'medium'
        };
        
        // Emit event to parent
        emit('taskCreated');
        
        // Show success message (you could add a toast notification here)
              } else {
        console.error('Failed to create task:', result.error);
      }
    } catch (error) {
      console.error('Error creating task:', error);
    } finally {
      isSubmitting.value = false;
    }
  };
  </script>