<script setup lang="ts">
import { ref, computed, watchEffect, onMounted } from 'vue'
import { useData } from '../composables/data'
import { useDateFormat } from '@vueuse/core';

const { theme, page, lang } = useData()

const date = computed(() => new Date(page.value.lastUpdated!))
const isoDatetime = computed(() => date.value.toISOString())
const datetime = ref('')
const formatedDate = useDateFormat(datetime, theme.value.lastUpdatedFormat, {locales: lang.value})

// set time on mounted hook to avoid hydration mismatch due to
// potential differences in timezones of the server and clients
onMounted(() => {
  watchEffect(() => {
    datetime.value = date.value.toLocaleString(lang.value)
  })
})
</script>

<template>
  <p class="VPLastUpdated">
    {{ theme.lastUpdatedText || 'Last updated' }}:
    <time v-if="!theme.lastUpdatedFormat" :datetime="isoDatetime">{{ datetime }}</time>
    <time v-else :datetime="isoDatetime">{{ formatedDate }}</time>
  </p>
</template>

<style scoped>
.VPLastUpdated {
  line-height: 24px;
  font-size: 14px;
  font-weight: 500;
  color: var(--vp-c-text-2);
}

@media (min-width: 640px) {
  .VPLastUpdated {
    line-height: 32px;
    font-size: 14px;
    font-weight: 500;
  }
}
</style>
