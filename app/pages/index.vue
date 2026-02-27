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

/* ---------------- ADD TODO ---------------- */
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
    color: 'green'
  })

  newTodo.value = ''
}

/* ---------------- DELETE ---------------- */
const removeTodo = (id: number) => {
  todos.value = todos.value.filter(t => t.id !== id)
}

/* ---------------- FILTER ---------------- */
const filteredTodos = computed(() => {
  if (filter.value === 'active')
    return todos.value.filter(t => !t.completed)

  if (filter.value === 'completed')
    return todos.value.filter(t => t.completed)

  return todos.value
})

/* ---------------- PROGRESS ---------------- */
const progress = computed(() => {
  if (!todos.value.length) return 0
  const completed = todos.value.filter(t => t.completed).length
  return Math.round((completed / todos.value.length) * 100)
})

/* ---------------- LOCAL STORAGE ---------------- */
onMounted(() => {
  const saved = localStorage.getItem('todos')
  if (saved) todos.value = JSON.parse(saved)
})

watch(todos, (val) => {
  localStorage.setItem('todos', JSON.stringify(val))
}, { deep: true })
</script>

<template>
  <UContainer class="max-w-2xl py-10">
    <UCard class="transition-colors">
      
      <template #header>
        <div class="flex justify-between items-center">
          <h1 class="text-2xl font-bold">📝 Smart Todo</h1>
          
          <!-- Dark Mode Toggle -->
          <ThemeToggle />
        </div>
      </template>

      <!-- ADD -->
      <div class="flex gap-2 mb-6">
        <UInput
          v-model="newTodo"
          placeholder="Enter task..."
          @keyup.enter="addTodo"
        />
        <UButton @click="addTodo">Add</UButton>
      </div>

      <!-- FILTER -->
      <div class="flex gap-2 mb-6">
        <UButton
          v-for="type in ['all','active','completed']"
          :key="type"
          :variant="filter === type ? 'solid' : 'outline'"
          @click="filter = type"
        >
          {{ type }}
        </UButton>
      </div>

      <!-- PROGRESS BAR -->
      <div class="mb-6">
        <UProgress :value="progress" />
        <p class="text-sm mt-2 text-gray-500">
          {{ progress }}% completed
        </p>
      </div>

      <!-- LIST -->
      <div v-if="filteredTodos.length === 0" class="text-center text-gray-400 py-6">
        No tasks here 👀
      </div>

      <div v-else class="space-y-3">
        <UCard
          v-for="todo in filteredTodos"
          :key="todo.id"
          :class="{ 'opacity-60': todo.completed }"
        >
          <div class="flex justify-between items-center">
            <div class="flex items-center gap-3">
              <UCheckbox v-model="todo.completed" />
              <span
                :class="[
                  'transition-colors',
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
              color="red"
              variant="ghost"
              @click="removeTodo(todo.id)"
            />
          </div>
        </UCard>
      </div>

    </UCard>
  </UContainer>
</template>