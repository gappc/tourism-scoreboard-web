<template>
  <div>
    <div>{{ metric.description }}</div>
    <div v-if="metric.loadError" class="text-error-500">
      {{ metric.loadError }}
    </div>
    <div v-if="metric.loading">
      Loading...
    </div>
    <div v-if="metric.loaded" :class="'flex'">
      <Gauge
        v-for="(gauge, index) in gauges"
        :key="index"
        :class="[
          'flex-1 text-center font-light text-5xl sm:text-2xl md:text-4xl border border-black mr-1',
          { 'ml-1': index !== 0 },
          { 'mr-1': index !== gauges.length - 1 }
        ]"
        :value="gauge.value"
        :title="gauge.title"
        :decimal-places="gauge.decimalPlaces"
        :unit="gauge.unit"
      ></Gauge>
    </div>
  </div>
</template>

<script>
import Gauge from '@/components/charts/gauge.vue'

import * as countryOfOrigin from '@/meta/elasticsearch/country-of-origin'

export default {
  components: {
    Gauge
  },
  computed: {
    gauges() {
      return [
        {
          title: 'Total',
          value: this.metric.results[0][countryOfOrigin.propTotal].value
        },
        {
          title: 'Different countries',
          value: this.metric.results[0][countryOfOrigin.propCountryCount].value
        }
      ]
    },
    metric() {
      return this.$store.getters['metrics/currentMetric']
    }
  },
  mounted() {
    return this.$store.dispatch('metrics/loadMetric', {
      metric: this.metric,
      queries: [
        countryOfOrigin.queryBuilder({ from: 0, to: 1575114421189, size: 15 })
      ],
      mappers: [countryOfOrigin.resultBuilder]
    })
  }
}
</script>