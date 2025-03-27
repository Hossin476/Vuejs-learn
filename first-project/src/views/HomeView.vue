<template>
  <div class="min-h-screen bg-gradient-to-br from-purple-50 to-indigo-50 py-12 px-4 sm:px-6">
    <div class="max-w-md mx-auto bg-white rounded-xl shadow-lg overflow-hidden">

      <div class="bg-gradient-to-r from-violet-500 to-purple-600 px-6 py-4">
        <h1 class="text-2xl font-bold text-white text-center">My Tasks</h1>
      </div>

      <div class="p-6">
        <div class="flex mb-6 group">
          <input v-model="newTask" @keyup.enter="addTask" placeholder="What needs to be done?"
            class="flex-1 p-3 border-2 border-r-0 border-gray-200 rounded-l-lg focus:outline-none focus:ring-2 focus:ring-violet-500 focus:border-transparent transition-all" />
          <button @click="addTask"
            class="px-4 bg-violet-600 cursor-pointer text-white rounded-r-lg hover:bg-violet-700 transition-colors duration-200 flex items-center justify-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 24 24" fill="none"
              stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <line x1="12" y1="5" x2="12" y2="19"></line>
              <line x1="5" y1="12" x2="19" y2="12"></line>
            </svg>
          </button>
        </div>

        <div class="flex justify-between mb-6">
          <button @click="filterTasks('all')" :class="[
            'px-4 py-2 rounded-lg cursor-pointer transition-all duration-200 font-medium text-sm',
            filter === 'all'
              ? 'bg-violet-600 text-white shadow-md'
              : 'bg-gray-100 text-gray-700 hover:bg-gray-200'
          ]">
            All
          </button>
          <button @click="filterTasks('active')" :class="[
            'px-4 py-2 rounded-lg transition-all cursor-pointer duration-200 font-medium text-sm',
            filter === 'active'
              ? 'bg-violet-600 text-white shadow-md'
              : 'bg-gray-100 text-gray-700 hover:bg-gray-200'
          ]">
            Active
          </button>
          <button @click="filterTasks('completed')" :class="[
            'px-4 py-2 rounded-lg transition-all duration-200 font-medium cursor-pointer text-sm',
            filter === 'completed'
              ? 'bg-violet-600 text-white shadow-md'
              : 'bg-gray-100 text-gray-700 hover:bg-gray-200'
          ]">
            Completed
          </button>
        </div>

        <div class="space-y-3">
          <TransitionGroup name="list">
            <div v-if="filteredTasks.length === 0" class="text-center py-8 text-gray-500">
              <svg xmlns="http://www.w3.org/2000/svg" class="h-12 w-12 mx-auto mb-2 text-gray-300" viewBox="0 0 24 24"
                fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <rect x="3" y="4" width="18" height="18" rx="2" ry="2"></rect>
                <line x1="16" y1="2" x2="16" y2="6"></line>
                <line x1="8" y1="2" x2="8" y2="6"></line>
                <line x1="3" y1="10" x2="21" y2="10"></line>
              </svg>
              <p class="font-medium">No tasks found</p>
              <p class="text-sm">{{ filter === 'all' ? 'Add a new task to get started' : 'Change filters to see more tasks' }}</p>
            </div>
            <TodoItem v-for="task in filteredTasks" :key="task.id" :title="task.title" :completed="task.completed"
              @toggle="toggleTask(task.id)" @remove="removeTask(task.id)" />
          </TransitionGroup>
        </div>

        <div v-if="tasks.length > 0"
          class="mt-6 pt-4 border-t border-gray-100 flex justify-between text-sm text-gray-500">
          <span>{{ activeTasksCount }} items left</span>
          <button v-if="completedTasksCount > 0" @click="clearCompleted"
            class="text-violet-600 hover:text-violet-800 transition-colors cursor-pointer">
            Clear completed
          </button>
        </div>
      </div>
    </div>

    <div class="mt-8 text-center text-sm text-gray-500">
      <p>Double-click to edit a task</p>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from 'vue'
import TodoItem from '../components/TodoItem.vue'

export default defineComponent({
  components: {
    TodoItem,
  },
  setup() {
    const newTask = ref('')
    const tasks = ref(JSON.parse(localStorage.getItem('tasks') || '[]'))
    const filter = ref('all')

    const saveTasks = () => {
      localStorage.setItem('tasks', JSON.stringify(tasks.value))
    }

    const addTask = () => {
      if (newTask.value.trim()) {
        tasks.value.push({
          id: Date.now(),
          title: newTask.value,
          completed: false,
        })
        newTask.value = ''
        saveTasks()
      }
    }

    const removeTask = (taskId: number) => {
      tasks.value = tasks.value.filter((task: { id: number; title: string; completed: boolean }) => task.id !== taskId)
      saveTasks()
    }

    const toggleTask = (taskId: number) => {
      const task = tasks.value.find((task: { id: number; title: string; completed: boolean }) => task.id === taskId)
      if (task) {
        task.completed = !task.completed
        saveTasks()
      }
    }

    const filterTasks = (status: string) => {
      filter.value = status
    }

    const clearCompleted = () => {
      tasks.value = tasks.value.filter((task: { id: number; title: string; completed: boolean }) => !task.completed)
      saveTasks()
    }

    const filteredTasks = computed(() => {
      if (filter.value === 'active') {
        return tasks.value.filter((task: { id: number; title: string; completed: boolean }) => !task.completed)
      } else if (filter.value === 'completed') {
        return tasks.value.filter((task: { id: number; title: string; completed: boolean }) => task.completed)
      }
      return tasks.value
    })

    const activeTasksCount = computed(() => {
      return tasks.value.filter((task: { id: number; title: string; completed: boolean }) => !task.completed).length
    })

    const completedTasksCount = computed(() => {
      return tasks.value.filter((task: { id: number; title: string; completed: boolean }) => task.completed).length
    })

    return {
      newTask,
      tasks: computed(() => tasks.value),
      addTask,
      removeTask,
      toggleTask,
      filterTasks,
      clearCompleted,
      filteredTasks,
      filter,
      activeTasksCount,
      completedTasksCount
    }
  },
})
</script>

<style>
.list-enter-active,
.list-leave-active {
  transition: all 0.3s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
</style>