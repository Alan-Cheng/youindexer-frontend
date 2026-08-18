<script setup>
const platforms = [
  { id: 'youtube', label: 'YouTube', icon: 'subscriptions', active: true },
  { id: 'threads', label: 'Threads', icon: 'chat', active: false, disabled: true },
  { id: 'instagram', label: 'Instagram', icon: 'camera_alt', active: false, disabled: true }
]

const bottomLinks = [
  { id: 'archive', label: '封存', icon: 'archive' },
  { id: 'trash', label: '垃圾桶', icon: 'delete' }
]

const emit = defineEmits(['select'])
</script>

<template>
  <aside
    class="bg-surface-container-low dark:bg-inverse-surface h-full w-64 border-r border-outline-variant shrink-0 hidden md:flex flex-col z-40"
  >
    <div class="flex flex-col gap-unit py-stack-md w-64 h-full">
      <div class="px-6 mb-4">
        <h2 class="font-title-md text-title-md font-semibold text-on-surface">平台</h2>
        <p class="font-body-md text-body-md text-on-surface-variant mt-1">篩選視圖</p>
      </div>

      <nav class="flex-1 flex flex-col gap-1">
        <a
          v-for="platform in platforms"
          :key="platform.id"
          href="#"
          :class="[
            'flex items-center gap-3 rounded-full px-4 py-3 mx-2 scale-95 active:scale-100 transition-transform font-label-lg text-label-lg',
            platform.active
              ? 'bg-secondary-container text-on-secondary-container'
              : 'text-on-surface-variant hover:bg-surface-container-high hover:bg-surface-container-highest transition-all',
            platform.disabled && 'opacity-50 pointer-events-none'
          ]"
          @click.prevent="emit('select', platform.id)"
        >
          <span
            class="material-symbols-outlined"
            :style="{ fontVariationSettings: platform.active ? '\'FILL\' 1' : '\'FILL\' 0' }"
          >
            {{ platform.icon }}
          </span>
          {{ platform.label }}
        </a>
      </nav>

      <div class="px-6 py-4">
        <button
          type="button"
          class="w-full flex justify-center items-center gap-2 border border-outline text-primary font-label-lg text-label-lg px-4 py-2 rounded-full hover:bg-surface-container-low transition-colors"
        >
          <span class="material-symbols-outlined text-lg">add</span>
          新增平台
        </button>
      </div>

      <div class="mt-auto border-t border-outline-variant pt-4 flex flex-col gap-1">
        <a
          v-for="link in bottomLinks"
          :key="link.id"
          href="#"
          class="flex items-center gap-3 text-on-surface-variant hover:bg-surface-container-high rounded-full px-4 py-3 mx-2 hover:bg-surface-container-highest transition-all scale-95 active:scale-100 transition-transform font-label-lg text-label-lg"
        >
          <span class="material-symbols-outlined">{{ link.icon }}</span>
          {{ link.label }}
        </a>
      </div>
    </div>
  </aside>
</template>
