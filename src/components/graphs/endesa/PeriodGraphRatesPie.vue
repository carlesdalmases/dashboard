<template>
  <GChart
    type="PieChart"
    :data="chartData"
    :options="chartOptions"
  />
</template>


<script>

import { GChart } from 'vue-google-charts'
import { find } from 'lodash'

export default {
  name: 'PeriodGraphRatesPie',
  components: {
    GChart
  },
  props: {
    graphTitle: {
      type: String,
      default () {
        return null;
      }
    },
    dataModel: {
      type: Array,
      default() {
        return [];
      }
    }
  },
  data () {
    return {
    }
  },
  computed: {
    chartData: function(){

      // // eslint-disable-next-line no-console

      let z = [];
      z.push(['Tarifa', 'kWh']);

      let x = find(this.dataModel, { 'rate': this.graphTitle});

      if(x)
      {
        switch (this.graphTitle){
          case '20A': {
            z.push(['p1', x.p1]);
            break;
          }
          case '20DHA': {
            z.push(['p1', x.p1]);
            z.push(['p2', x.p2]);
            break;
          }
          case '20DHS': {
            z.push(['P1', x.p1]);
            z.push(['P2', x.p2]);
            z.push(['P3', x.p3]);
            break;
          }
        }
      }
      return z;
    },
    chartOptions () {
      return {
        title: this.graphTitle,
        height: 200,
        width: 200,
        // is3D: true,
        pieHole: 0.8,
        pieSliceText: 'none',
        colors: ['#ff3860', '#ffdd57', '#23d160'],
        legend: {
          position: 'none'
        }
      };
    }
  }, //Fi de computed
  watch: {
  },
  methods: {
  } //Fi de methods()
} //export

</script>

<style/>
