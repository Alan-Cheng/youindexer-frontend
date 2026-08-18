<script setup>
import { useRouter } from 'vue-router'

import TopNavBar from '@/components/TopNavBar.vue'
import LogoHero from '@/components/LogoHero.vue'
import SearchBar from '@/components/SearchBar.vue'
import SearchActions from '@/components/SearchActions.vue'
import TrendingAnalysis from '@/components/TrendingAnalysis.vue'
import AppFooter from '@/components/AppFooter.vue'

const router = useRouter()

function goToResults(query) {
  router.push({ name: 'search-results', query: { q: query } })
}

function handleTagSelect(label) {
  goToResults(label)
}
</script>

<template>
  <div class="min-h-screen flex flex-col">
    <TopNavBar />

    <main
      class="flex-grow flex flex-col items-center justify-center px-margin-mobile md:px-margin-desktop py-12 md:py-24 max-w-[1280px] mx-auto w-full"
    >
      <LogoHero />
      <div class="w-full max-w-[680px] mb-stack-md">
        <SearchBar @search="goToResults" />
      </div>
      <SearchActions @action="goToResults" />
      <TrendingAnalysis @select="handleTagSelect" />
    </main>

    <AppFooter />
  </div>
</template>
