<script setup lang="ts">
const colorMode = useColorMode()

const toggleColorMode = () => {
  colorMode.preference =
    colorMode.value === 'dark' ? 'light' : 'dark'
}
</script>

<template>
  <UApp>
    <UHeader>
      <template #left>
        <h1 class="font-bold">📝 Todo App</h1>
      </template>

      <template #right>
        <UButton
          :icon="colorMode.value === 'dark'
            ? 'i-heroicons-sun'
            : 'i-heroicons-moon'"
          variant="ghost"
          @click="toggleColorMode"
        />
      </template>
    </UHeader>

    <UMain>
      <NuxtPage />
    </UMain>
  </UApp>
</template>