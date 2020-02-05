<template>
  <div class="PowerChart">
    <line-chart :chart-data="datacollection" height="150%"></line-chart>
  </div>
</template>

<script>
import LineChart from './LineChart.js'

export default {
  name: 'PowerChart',
  data () {
  },
  props: {
    datacollection: Object
  },
  components: {
    LineChart
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">

</style>
