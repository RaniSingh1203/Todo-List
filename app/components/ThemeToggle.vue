<script setup lang="ts">
const colorMode = useColorMode()

const toggleTheme = () => {
  colorMode.preference =
    colorMode.preference === 'dark' ? 'light' : 'dark'
}
</script>

<template>
  <UButton
   :icon="colorMode.value === 'dark'
            ? 'i-heroicons-sun'
            : 'i-heroicons-moon'"
          variant="ghost"
          @click="toggleTheme"
  />
</template>