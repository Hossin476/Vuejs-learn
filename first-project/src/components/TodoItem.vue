<template>
  <div
    class="group bg-white border border-gray-100 rounded-lg shadow-sm hover:shadow-md transition-all duration-200 overflow-hidden"
    :class="{ 'bg-gray-50': completed }">
    <div class="flex items-center p-4">

      <div class="relative flex-shrink-0">
        <input type="checkbox" :checked="completed" @change="toggleCompletion" class="sr-only peer"
          :id="`task-${_uid}`" />
        <label :for="`task-${_uid}`"
          class="flex h-5 w-5 cursor-pointer items-center justify-center rounded-full border border-gray-300 bg-white ring-offset-background transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:cursor-not-allowed disabled:opacity-50 peer-checked:border-violet-500 peer-checked:bg-violet-500 peer-checked:text-white hover:border-violet-400">
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor"
            stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" class="h-3 w-3 text-white"
            :class="completed ? 'opacity-100' : 'opacity-0'">
            <polyline points="20 6 9 17 4 12"></polyline>
          </svg>
        </label>
      </div>

      <span class="ml-3 flex-1 text-gray-800 transition-all duration-200"
        :class="{ 'line-through text-gray-400': completed }">
        {{ title }}
      </span>

      <button @click="removeItem"
        class="ml-2 flex-shrink-0 cursor-pointer rounded-full p-1.5 text-gray-400 opacity-0 transition-opacity duration-200 hover:bg-red-50 hover:text-red-500 group-hover:opacity-100 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2"
        aria-label="Delete task">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 24 24" fill="none" stroke="currentColor"
          stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <polyline points="3 6 5 6 21 6"></polyline>
          <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
          <line x1="10" y1="11" x2="10" y2="17"></line>
          <line x1="14" y1="11" x2="14" y2="17"></line>
        </svg>
      </button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import type {PropType} from 'vue'

export default defineComponent({
  name: 'TodoItem',
  props: {
    title: {
      type: String as PropType<string>,
      required: true,
    },
    completed: {
      type: Boolean as PropType<boolean>,
      required: true,
    },
  },
  methods: {
    toggleCompletion() {
      this.$emit('toggle')
    },
    removeItem() {
      this.$emit('remove')
    },
  },
})
</script>