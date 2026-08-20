<script setup>
import { onBeforeUnmount, onMounted, ref } from 'vue'
import { createElement } from 'react'
import { createRoot } from 'react-dom/client'
import { ThinkingOrb as ReactThinkingOrb } from 'thinking-orbs'

const props = defineProps({
  speed: {
    type: Number,
    default: () => 0.3 + Math.random() * 0.3
  }
})

const orbHost = ref(null)
let root = null

onMounted(() => {
  root = createRoot(orbHost.value)
  root.render(
    createElement(ReactThinkingOrb, {
      state: 'solving',
      size: 64,
      speed: props.speed,
      'aria-label': '搜尋處理中'
    })
  )
})

onBeforeUnmount(() => {
  root?.unmount()
  root = null
})
</script>

<template>
  <div ref="orbHost" class="flex items-center justify-center" aria-live="polite" />
</template>
