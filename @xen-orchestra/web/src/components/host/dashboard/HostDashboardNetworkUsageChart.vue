<template>
  <UiCard>
    <UiCardTitle>
      {{ t('network-throughput') }}
      <template #description>{{ t('last-week') }}</template>
    </UiCardTitle>
    <VtsLoadingHero v-if="loading || data === null" type="card" />
    <VtsErrorNoDataHero v-else-if="error" type="card" />
    <VtsLinearChart v-else :data="networkUsage" :max-value :value-formatter="byteFormatter" />
  </UiCard>
</template>

<script lang="ts" setup>
import type { LinearChartData } from '@core/types/chart.ts'
import VtsErrorNoDataHero from '@core/components/state-hero/VtsErrorNoDataHero.vue'
import VtsLoadingHero from '@core/components/state-hero/VtsLoadingHero.vue'
import UiCard from '@core/components/ui/card/UiCard.vue'
import UiCardTitle from '@core/components/ui/card-title/UiCardTitle.vue'
import { formatSizeRaw } from '@core/utils/size.util.ts'
import type { XapiHostStats } from '@vates/types/common'
import { computed, defineAsyncComponent } from 'vue'
import { useI18n } from 'vue-i18n'

const { data } = defineProps<{
  data: XapiHostStats | null
  loading: boolean
  error?: string
}>()

const VtsLinearChart = defineAsyncComponent(() => import('@core/components/linear-chart/VtsLinearChart.vue'))

const { t } = useI18n()

const networkUsage = computed<LinearChartData>(() => {
  if (!data?.stats?.pifs) {
    return []
  }

  const timestamps = Array.from(
    { length: data.stats.pifs.rx['0'].length },
    (_, i) => data.endTimestamp * 1000 - ((data.stats.pifs?.rx?.['0'].length ?? 0) - 1 - i) * data.interval * 1000
  )

  const rxSeries = [
    {
      label: t('network-upload'),
      data: timestamps.map((timestamp, index) => ({
        timestamp,
        value: Object.values(data.stats.pifs?.rx ?? {}).reduce((sum, values) => sum + (values[index] ?? NaN), 0),
      })),
    },
  ]

  const txSeries = [
    {
      label: t('network-upload'),
      data: timestamps.map((timestamp, index) => ({
        timestamp,
        value: Object.values(data.stats.pifs?.tx ?? {}).reduce((sum, values) => sum + (values[index] ?? NaN), 0),
      })),
    },
  ]

  return [...txSeries, ...rxSeries]
})

const maxValue = computed(() => {
  const values = networkUsage.value.flatMap(series => series.data.map(item => item.value || 0))

  if (values.length === 0) {
    return 100
  }

  const maxUsage = Math.max(...values) * 1.2

  return Math.ceil(maxUsage / 50) * 50
})

const byteFormatter = (value: number | null) => {
  if (value === null) {
    return ''
  }

  const size = formatSizeRaw(value, 1)

  return `${size.value} ${size.prefix}`
}
</script>
