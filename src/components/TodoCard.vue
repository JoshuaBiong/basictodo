<script setup>
const props = defineProps({
  todo: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['toggle', 'remove'])

const handleToggle = () => {
  emit('toggle', props.todo.id)
}

const handleRemove = () => {
  emit('remove', props.todo.id)
}
</script>

<template>
  <li  class="group flex items-center justify-between p-5 bg-white rounded-xl shadow-sm hover:shadow-md transition-all border border-gray-100">
    <div class="flex items-center gap-4">
      <input
        type="checkbox"
        :checked="todo.completed"
        @change="handleToggle"
        class="w-6 h-6 rounded-lg border-gray-300 text-blue-500 focus:ring-blue-500 cursor-pointer transition-all"
      />
      <span
        :class="[
          'text-lg text-gray-700 transition-all',
          todo.completed ? 'line-through text-gray-400' : ''
        ]"
      >
        {{ todo.text }}
      </span>
    </div>
    <button
      @click="handleRemove"
      class="text-red-500 text-2xl font-bold opacity-0 group-hover:opacity-100 px-2 hover:text-red-600 transition-all transform hover:scale-110"
    >
      ×
    </button>
  </li>
</template> 