<script setup>
import { ref } from 'vue'

const props = defineProps({
  todo: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['toggle', 'remove', 'update'])
const isEditing = ref(false)
const editedText = ref('')

const handleToggle = () => {
  emit('toggle', props.todo.id)
}

const handleRemove = () => {
  emit('remove', props.todo.id)
}

const startEditing = () => {
  editedText.value = props.todo.text
  isEditing.value = true
}

const saveEdit = () => {
  if (editedText.value.trim() && editedText.value !== props.todo.text) {
    emit('update', props.todo.id, editedText.value)
  }
  isEditing.value = false
}

const cancelEdit = () => {
  isEditing.value = false
  editedText.value = props.todo.text
}
</script>

<template>
  <li class="group flex items-center justify-between p-5 bg-white rounded-xl shadow-sm hover:shadow-md transition-all border border-gray-100">
    <div class="flex items-center gap-4 flex-grow">
      <input
        type="checkbox"
        :checked="todo.completed"
        @change="handleToggle"
        class="w-6 h-6 rounded-lg border-gray-300 text-blue-500 focus:ring-blue-500 cursor-pointer transition-all"
      />
      <div v-if="!isEditing" class="flex-grow">
        <span
          :class="[
            'text-lg text-gray-700 transition-all cursor-pointer',
            todo.completed ? 'line-through text-gray-400' : ''
          ]"
          @dblclick="startEditing"
        >
          {{ todo.text }}
        </span>
      </div>
      <input
        v-else
        v-model="editedText"
        @keyup.enter="saveEdit"
        @keyup.esc="cancelEdit"
        @blur="saveEdit"
        type="text"
        class="flex-grow text-lg text-gray-700 border-2 border-blue-500 rounded-lg px-2 py-1 focus:outline-none focus:ring-2 focus:ring-blue-200"
        ref="editInput"
      />
    </div>
    <div class="flex items-center gap-2">
      <button
        v-if="!isEditing"
        @click="startEditing"
        class="text-blue-500 text-base opacity-0 group-hover:opacity-100 px-2 hover:text-blue-600 transition-all transform hover:scale-110"
      >
      edit
      </button>
      <button
        @click="handleRemove"
        class="text-red-500 text-2xl font-bold opacity-0 group-hover:opacity-100 px-2 hover:text-red-600 transition-all transform hover:scale-110"
      >
        ×
      </button>
    </div>
  </li>
</template> 