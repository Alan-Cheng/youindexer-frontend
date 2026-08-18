<script setup>
defineProps({
  alt: {
    type: String,
    default: 'SocialIntel Logo'
  }
})
</script>

<template>
  <div class="mb-stack-lg flex flex-col items-center">
    <img
      :alt="alt"
      class="w-32 h-32 md:w-48 md:h-48 object-contain mb-4"
      src="https://lh3.googleusercontent.com/aida/AP1WRLuGSgyR1Ccmwm0r9F9GHFylCyX_qtUSR-05SGyeEeDARHaKa3Pi---iRVwE2OHXYwr3cMYaKrJ3sZrU2wFb_iST_kz7jn_dqkgWbonapZlQIlrBvBycYw9btrROacBWYe_AMDK_0HlB2zw2F5hqLpryqOpYGgFXhJuzXA74hB6PbKj6w1fWKFa8BAd7u21BSPTDwnLibA8iRNq7ocE77fKAA-pjyAwTJr_A0sIbz5RuVgXB7T0DI7WGyDw"
    />
    <h1 class="font-display-lg text-display-lg text-on-surface hidden">SocialIntel</h1>
  </div>
</template>
