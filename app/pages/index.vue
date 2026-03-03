<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'

interface Todo {
  id: number
  text: string
  completed: boolean
}

const newTodo = ref('')
const todos = ref<Todo[]>([])
const filter = ref<'all' | 'active' | 'completed'>('all')

const toast = useToast()

const addTodo = () => {
  if (!newTodo.value.trim()) return

  todos.value.push({
    id: Date.now(),
    text: newTodo.value,
    completed: false
  })

  toast.add({
    title: 'Task Added 🎉',
    description: newTodo.value,
    color: 'success'
  })

  newTodo.value = ''
}

const removeTodo = (id: number) => {
  todos.value = todos.value.filter(t => t.id !== id)

  toast.add({
    title: 'Task Deleted',
    color: 'error'
  })
}


const filteredTodos = computed(() => {
  if (filter.value === 'active')
    return todos.value.filter(t => !t.completed)

  if (filter.value === 'completed')
    return todos.value.filter(t => t.completed)

  return todos.value
})


const progress = computed(() => {
  if (!todos.value.length) return 0

  const completed = todos.value.filter(t => t.completed).length

  return Math.min(
    100,
    Math.round((completed / todos.value.length) * 100)
  )
})


onMounted(() => {
  if (process.client) {
    const saved = localStorage.getItem('todos')
    if (saved) {
      todos.value = JSON.parse(saved)
    }
  }
})

watch(
  todos,
  (val) => {
    if (process.client) {
      localStorage.setItem('todos', JSON.stringify(val))
    }
  },
  { deep: true }
)
</script>

<template>
  <UContainer class="max-w-2xl py-10">
    <UCard>

 
      <template #header>
        <div class="flex justify-between items-center">
          <h1 class="text-2xl font-bold">📝TodoApp</h1>
          <ThemeToggle />
        </div>
      </template>

      <div class="flex gap-2 mb-6">
        <UInput
          v-model="newTodo"
          placeholder="Enter task..."
          @keyup.enter="addTodo"
        />
        <UButton @click="addTodo">Add</UButton>
      </div>

    
      <div class="flex gap-2 mb-6">
        <UButton
          v-for="type in ['all','active','completed'] as const"
          :key="type"
          :variant="filter === type ? 'solid' : 'soft'"
          @click="filter = type"
        >
          {{ type }}
        </UButton>
      </div>

   
<div class="mb-6">
  <AppProgress :value="progress" />
</div>
      <div v-if="filteredTodos.length === 0" class="text-center text-gray-400 py-6">
        No tasks here 👀
      </div>

      <div v-else class="space-y-3">
        <UCard
          v-for="todo in filteredTodos"
          :key="todo.id"
          :class="{ 'opacity-60': todo.completed }">
          <div class="flex justify-between items-center">
            <div class="flex items-center gap-3">
              <UCheckbox v-model="todo.completed" />
              <span
                :class="[
                  todo.completed
                    ? 'line-through text-gray-400'
                    : 'text-gray-800 dark:text-gray-200'
                ]"
              >
                {{ todo.text }}
              </span>
            </div>

            <UButton
              icon="i-heroicons-trash"
              color="error"
              variant="ghost"
              @click="removeTodo(todo.id)"
            />
          </div>
        </UCard>
      </div>

    </UCard>
  </UContainer>
</template>