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

import * as accomodationCategory from '@/meta/elasticsearch/accomodation-category'

export default {
  components: {
    Gauge
  },
  computed: {
    gauges() {
      return [
        {
          title: 'Total',
          value: this.metric.results[0][accomodationCategory.propTotal].value
        },
        {
          title: 'Private accomodations',
          value: this.metric.results[0][accomodationCategory.propPrivate].value
        },
        {
          title: 'Farms',
          value: this.metric.results[0][accomodationCategory.propFarms].value
        },
        {
          title: 'Others',
          value: this.metric.results[0][accomodationCategory.propOthers].value
        },
        {
          title: 'Hotel 1-3 stars',
          value: this.metric.results[0][accomodationCategory.propHotel1to3]
            .value
        },
        {
          title: 'Hotel 4-5 stars',
          value: this.metric.results[0][accomodationCategory.propHotel4to5]
            .value
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
        accomodationCategory.queryBuilder({ from: 0, to: 1575114421189 })
      ],
      mappers: [accomodationCategory.resultBuilder]
    })
  }
}
</script>