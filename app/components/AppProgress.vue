<script setup lang="ts">
import { computed } from 'vue'

const props = defineProps<{
  value: number
}>()

const safeValue = computed(() =>
  Math.min(100, Math.max(0, props.value))
)

const barColor = computed(() => {
  if (safeValue.value < 30) return 'bg-red-500'
  if (safeValue.value < 70) return 'bg-yellow-400'
  return 'bg-green-500'
})
</script>

<template>
  <div class="w-full">
    <div class="w-full bg-gray-200 rounded-full h-6 overflow-hidden relative">
    
      <div
        :class="[
          'h-6 flex items-center justify-center text-xs font-semibold text-white transition-all duration-500 ease-in-out',
          barColor
        ]"
        :style="{ width: safeValue + '%' }"
      >
        {{ safeValue }}%
      </div>
    </div>
  </div>
</template>