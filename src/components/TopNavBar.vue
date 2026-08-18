<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  showSearch: {
    type: Boolean,
    default: false
  },
  searchQuery: {
    type: String,
    default: ''
  }
})

const emit = defineEmits(['search'])

const navItems = [
  { label: '控制台', href: '#', active: true },
  { label: '數據分析', href: '#', active: false },
  { label: '深度洞察', href: '#', active: false }
]

const iconButtons = [
  { icon: 'notifications', label: 'Notifications' },
  { icon: 'settings', label: 'Settings' }
]

const mobileMenuOpen = ref(false)
const searchText = ref(props.searchQuery)

watch(
  () => props.searchQuery,
  (value) => {
    searchText.value = value
  }
)

function toggleMobileMenu() {
  mobileMenuOpen.value = !mobileMenuOpen.value
}

function handleSearchSubmit() {
  const trimmed = searchText.value.trim()
  if (trimmed) {
    emit('search', trimmed)
  }
}
</script>

<template>
  <header class="w-full bg-surface dark:bg-inverse-surface border-b border-outline-variant dark:border-outline shadow-sm z-50">
    <div
      class="flex justify-between items-center w-full px-margin-mobile md:px-margin-desktop h-16"
    >
      <div class="flex items-center gap-4 md:gap-6">
        <div class="font-title-lg text-title-lg font-bold text-google-blue">SocialIntel</div>
        <nav class="hidden md:flex gap-6">
          <a
            v-for="item in navItems"
            :key="item.label"
            :href="item.href"
            :class="[
              'font-body-md text-body-md pb-1 transition-colors cursor-pointer active:opacity-80 transition-opacity',
              item.active
                ? 'text-primary font-bold border-b-2 border-primary'
                : 'text-on-surface-variant dark:text-surface-variant hover:bg-surface-container dark:hover:bg-surface-container-highest'
            ]"
          >
            {{ item.label }}
          </a>
        </nav>
      </div>

      <div class="flex items-center gap-2 md:gap-4">
        <!-- Header Search -->
        <form
          v-if="showSearch"
          class="hidden md:flex items-center bg-surface-container rounded-full px-4 py-2"
          @submit.prevent="handleSearchSubmit"
        >
          <span class="material-symbols-outlined text-on-surface-variant mr-2 text-[20px]">search</span>
          <input
            v-model="searchText"
            type="text"
            class="bg-transparent border-none focus:ring-0 text-body-md font-body-md text-on-surface p-0 w-48 outline-none"
            placeholder="搜尋提及內容..."
          />
        </form>

        <a
          href="#"
          class="font-body-md text-body-md text-primary dark:text-inverse-primary hover:bg-surface-container dark:hover:bg-surface-container-highest transition-colors cursor-pointer active:opacity-80 transition-opacity hidden md:block"
        >
          說明
        </a>
        <div class="flex gap-1 md:gap-2">
          <button
            v-for="btn in iconButtons"
            :key="btn.icon"
            :aria-label="btn.label"
            class="p-2 rounded-full hover:bg-surface-container transition-colors text-primary dark:text-inverse-primary"
          >
            <span class="material-symbols-outlined">{{ btn.icon }}</span>
          </button>
        </div>
        <img
          alt="User Profile"
          class="w-8 h-8 rounded-full bg-surface-variant"
          src="https://lh3.googleusercontent.com/aida-public/AB6AXuCB1OZRxfDJjLeQp3njguggjo3A-8bP54gdmAy7kQkRo5MGHcFVsz2k5nX63cAVE8mYhOwUxk_jB-SPpGgBcvrcjD2rnfgKg9Gwb1JTo9p4O4xu6F0aA0Hsas5qOaOiflEF6dE4OB2kQ2YTkzFj3fNep4-TJn60PojBGMsl0nzfQf88gRkJaJcUl1LG0Qj4WywLO63IKhVVuDMJa8TkPvg1FvKeE3dorUaI0T1_OfQACTtazNFIiLex"
        />
        <button
          type="button"
          aria-label="Open menu"
          class="md:hidden p-2 rounded-full hover:bg-surface-container transition-colors text-primary dark:text-inverse-primary"
          @click="toggleMobileMenu"
        >
          <span class="material-symbols-outlined">menu</span>
        </button>
      </div>
    </div>
  </header>

  <!-- Mobile menu -->
  <nav
    v-if="mobileMenuOpen"
    class="md:hidden w-full bg-surface dark:bg-inverse-surface border-b border-outline-variant"
  >
    <div class="flex flex-col gap-2 px-margin-mobile py-stack-sm max-w-[1280px] mx-auto">
      <a
        v-for="item in navItems"
        :key="item.label"
        :href="item.href"
        :class="[
          'font-body-md text-body-md py-2 px-3 rounded-lg transition-colors',
          item.active
            ? 'text-primary font-bold bg-surface-container dark:bg-surface-container-highest'
            : 'text-on-surface-variant dark:text-surface-variant hover:bg-surface-container dark:hover:bg-surface-container-highest'
        ]"
      >
        {{ item.label }}
      </a>
      <a
        href="#"
        class="font-body-md text-body-md text-primary dark:text-inverse-primary py-2 px-3 rounded-lg hover:bg-surface-container dark:hover:bg-surface-container-highest transition-colors"
      >
        說明
      </a>
    </div>
  </nav>
</template>
