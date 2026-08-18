<script>
export default {
  name: 'SearchResultsView'
}
</script>

<script setup>
import { computed, onMounted, onUnmounted, ref, watch } from 'vue'
import { useRoute, useRouter } from 'vue-router'

import TopNavBar from '@/components/TopNavBar.vue'
import PlatformSideNav from '@/components/PlatformSideNav.vue'
import SearchResultCard from '@/components/SearchResultCard.vue'
import SearchResultSkeletonCard from '@/components/SearchResultSkeletonCard.vue'
import { createKeywordSearchJob, streamKeywordSearchJobEvents } from '@/api/youtube.js'

const route = useRoute()
const router = useRouter()

const job = ref(null)
const error = ref('')
const isCreating = ref(false)
const cleanupSSE = ref(null)

function getQuery() {
  const q = route.query.q
  return Array.isArray(q) ? q[0] : q || ''
}

const query = computed(getQuery)

function handleHeaderSearch(newQuery) {
  router.push({ name: 'search-results', query: { q: newQuery } })
}

const videoList = computed(() => {
  if (!job.value?.videos) return []
  return Object.values(job.value.videos).sort((a, b) => {
    // Loading first, then matched, then others
    const order = { loading: 0, matched: 1, no_match: 2, failed: 3 }
    return (order[a.status] ?? 9) - (order[b.status] ?? 9)
  })
})

const progressText = computed(() => {
  if (!job.value) return ''
  const { status, completed_count, video_count, matched_count } = job.value
  if (status === 'completed') {
    return `搜尋完成，可能提及您搜尋內容的影片有 ${matched_count} 部`
  }
  if (status === 'failed') {
    return '搜尋失敗'
  }
  return `處理中 ${completed_count || 0} / ${video_count || 0} 部影片...`
})

const showSkeletons = computed(() => {
  if (error.value) return false
  if (!job.value) return true
  return job.value.status === 'processing' && videoList.value.length === 0
})

const skeletonCount = computed(() => job.value?.video_count || 6)

function goToVideo(videoId) {
  if (!job.value?.task_id || !videoId) return
  router.push({
    name: 'video-insight',
    query: { task_id: job.value.task_id, video_id: videoId, q: query.value }
  })
}

async function startSearch() {
  const q = query.value.trim()
  if (!q) return

  // Reset state for a new query
  if (cleanupSSE.value) {
    cleanupSSE.value()
    cleanupSSE.value = null
  }
  job.value = null
  error.value = ''
  isCreating.value = true

  try {
    const initial = await createKeywordSearchJob(q, 'zh-TW', 5)
    job.value = initial
    isCreating.value = false

    cleanupSSE.value = streamKeywordSearchJobEvents(initial.task_id, {
      onSnapshot: (data) => {
        job.value = data
      },
      onUpdate: (data) => {
        job.value = data
      },
      onError: (detail) => {
        error.value = detail || '串流發生錯誤'
      }
    })
  } catch (err) {
    error.value = err.message || '無法建立搜尋任務'
    isCreating.value = false
  }
}

onMounted(startSearch)

onUnmounted(() => {
  if (cleanupSSE.value) {
    cleanupSSE.value()
    cleanupSSE.value = null
  }
})

watch(() => route.query.q, startSearch)
</script>

<template>
  <div class="h-screen overflow-hidden flex flex-col bg-surface text-on-surface font-body-lg">
    <TopNavBar :show-search="true" :search-query="query" @search="handleHeaderSearch" />

    <div class="flex flex-1 overflow-hidden">
      <PlatformSideNav />

      <main class="flex-1 overflow-y-auto bg-surface-container-low">
        <div class="max-w-[1024px] mx-auto p-margin-mobile md:p-margin-desktop">
          <!-- Search Header -->
          <div class="mb-stack-lg">
            <h1
              class="font-headline-lg-mobile md:font-headline-lg text-headline-lg-mobile md:text-headline-lg text-on-surface mb-2"
            >
              「{{ query || '...' }}」的搜尋結果
            </h1>
            <p v-if="progressText" class="font-body-md text-body-md text-on-surface-variant">
              {{ progressText }}
            </p>
          </div>

          <!-- Progress bar -->
          <div
            v-if="job && job.status === 'processing'"
            class="w-full h-1.5 bg-surface-container-high rounded-full overflow-hidden mb-stack-lg"
          >
            <div
              class="h-full bg-primary transition-all duration-500"
              :style="{
                width: job.video_count ? `${(job.completed_count / job.video_count) * 100}%` : '0%'
              }"
            />
          </div>

          <!-- Error -->
          <div
            v-if="error"
            class="mb-stack-lg p-stack-md bg-error-container text-on-error-container rounded-xl border border-error/20"
          >
            <p class="font-body-md text-body-md">{{ error }}</p>
            <button
              type="button"
              class="mt-stack-sm px-4 py-2 bg-error text-on-error rounded-DEFAULT font-label-lg text-label-lg hover:opacity-90 transition-opacity"
              @click="startSearch"
            >
              重試
            </button>
          </div>

          <!-- Bento Grid -->
          <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-gutter items-start">
            <template v-if="showSkeletons">
              <SearchResultSkeletonCard v-for="n in skeletonCount" :key="`skeleton-${n}`" />
            </template>
            <template v-else>
              <SearchResultCard
                v-for="video in videoList"
                :key="video.metadata.video_id"
                :video="video"
                :status="video.status"
                @click="goToVideo"
              />
            </template>
          </div>
          <p v-if="showSkeletons" class="sr-only" role="status">搜尋載入中</p>

          <!-- Empty state -->
          <div
            v-if="!isCreating && !error && videoList.length === 0 && job?.status === 'completed'"
            class="mt-stack-lg text-center"
          >
            <p class="font-body-lg text-body-lg text-on-surface-variant">
              未找到任何影片結果。
            </p>
          </div>
        </div>

        <footer
          class="mt-16 border-t border-outline-variant py-stack-md flex flex-col sm:flex-row justify-between items-center max-w-[1024px] mx-auto text-on-surface-variant font-label-sm text-label-sm px-margin-mobile md:px-0"
        >
          <p>© 2026 YouIndexer. 保留所有權利。</p>
          <div class="flex gap-4 mt-4 sm:mt-0">
            <a class="hover:text-primary transition-colors opacity-100 hover:opacity-80" href="#">隱私權</a>
            <a class="hover:text-primary transition-colors opacity-100 hover:opacity-80" href="#">條款</a>
            <a class="hover:text-primary transition-colors opacity-100 hover:opacity-80" href="#">網站地圖</a>
          </div>
        </footer>
      </main>
    </div>
  </div>
</template>
