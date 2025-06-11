<script setup>
import { ref, onMounted, watch, computed } from 'vue'
import TodoCard from '../components/TodoCard.vue'

const todos = ref([])
const newTodo = ref('')
const filter = ref('all')

onMounted(() => {
  const savedTodos = localStorage.getItem('todos')
  if (savedTodos) {
    todos.value = JSON.parse(savedTodos)
  }
})

watch(todos, (newTodos) => {
  localStorage.setItem('todos', JSON.stringify(newTodos))
}, { deep: true })

const addTodo = () => {
  if (newTodo.value.trim()) {
    todos.value.push({
      id: Date.now(),
      text: newTodo.value.trim(),
      completed: false,
      createdAt: new Date().toISOString()
    })
    newTodo.value = ''
  }
}

const removeTodo = (id) => {
  todos.value = todos.value.filter(todo => todo.id !== id)
}

const toggleTodo = (id) => {
  const todo = todos.value.find(todo => todo.id === id)
  if (todo) {
    todo.completed = !todo.completed
  }
}

// Filtering function
const filteredTodos = computed(() => {
  let filtered = []
  switch (filter.value) {
    case 'active':
      filtered = todos.value.filter(todo => !todo.completed)
      break
    case 'completed':
      filtered = todos.value.filter(todo => todo.completed)
      break
    default:
      filtered = todos.value
  }
  return filtered.sort((a, b) => new Date(b.createdAt) - new Date(a.createdAt))
})

const clearCompleted = () => {
  todos.value = todos.value.filter(todo => !todo.completed)
}

const updateTodo = (id, newText) => {
  const todo = todos.value.find(todo => todo.id === id)
  if (todo && newText.trim()) {
    todo.text = newText.trim()
  }
}

</script>

<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 py-12 px-4 sm:px-6 lg:px-8">
    <div class="max-w-7xl mx-auto">
      <div class="bg-white rounded-2xl shadow-xl p-8 backdrop-blur-sm bg-opacity-90">
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
          <!-- left side -->
          <div class="space-y-8">
            <h1 class="text-4xl font-extrabold text-gray-800 bg-gradient-to-r from-blue-600 to-indigo-600 bg-clip-text text-transparent">
              My Todo List
            </h1>

            <div class="flex flex-col gap-4">
              <input
                v-model="newTodo"
                @keyup.enter="addTodo"
                type="text"
                placeholder="Add a new task..."
                class="w-full px-6 py-4 text-lg border-2 border-gray-200 rounded-xl focus:outline-none focus:border-blue-500 focus:ring-2 focus:ring-blue-200 transition-all shadow-sm"
              />
              <button
                @click="addTodo"
                class="w-full px-8 py-4 bg-gradient-to-r from-blue-500 to-indigo-500 text-white font-bold rounded-xl hover:from-blue-600 hover:to-indigo-600 transform hover:scale-105 transition-all shadow-lg"
              >
                Add Task
              </button>
            </div>

            <!-- filtering -->
            <div class="flex gap-3">
              <button
                v-for="option in ['all', 'active', 'completed']"
                :key="option"
                @click="filter = option"
                :class="[
                  'px-6 py-3 rounded-xl font-medium transition-all transform hover:scale-105',
                  filter === option
                    ? 'bg-gradient-to-r from-blue-500 to-indigo-500 text-white shadow-lg'
                    : 'bg-gray-100 text-gray-700 hover:bg-gray-200 shadow-sm'
                ]"
              >
                {{ option.charAt(0).toUpperCase() + option.slice(1) }}
              </button>
            </div>
            <button
              v-if="todos.some(todo => todo.completed)"
              @click="clearCompleted"
              class="w-full px-8 py-4 bg-gradient-to-r from-gray-100 to-gray-200 text-gray-600 rounded-xl hover:from-gray-200 hover:to-gray-300 transition-all transform hover:scale-105 shadow-lg font-medium"
            >
              Clear Completed
            </button>
          </div>
          <!-- right side -->
          <div class="lg:border-l lg:border-gray-200 lg:pl-8">
            <h2 class="text-2xl font-bold text-gray-800 mb-6">Tasks</h2>
            <ul class="space-y-4">
              <TodoCard
                v-for="todo in filteredTodos"
                :key="todo.id"
                :todo="todo"
                @toggle="toggleTodo"
                @remove="removeTodo"
                @update="updateTodo"

              />
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>



