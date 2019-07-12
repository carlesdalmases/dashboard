<template>
  <GChart
    type="LineChart"
    :data="chartData"
    :options="chartOptions"
  />
</template>


<script>

import { GChart } from 'vue-google-charts'
import { map } from 'lodash'
import { parse,setHours } from 'date-fns'

export default {
  name: 'PeriodGraphHourlyLine',
  components: {
    GChart
  },
  props: {
    dataModel: {
      type: Array,
      default() {
        return [];
      }
    }
  },
  data: function(){
    return {
      chartOptions: {
        chart: {
          title: "Consum pel període",
          subtitle: "en kW/h",
          width: 900,
          height: 500,
          hAxis: {
            format: 'M/d/yy',
            gridlines: {count: 15}
          },
          vAxis: {
            gridlines: {color: 'none'},
            minValue: 0
          }
        }
      }
    }
  },
  computed: {
    chartData: function(){

      // eslint-disable-next-line no-console
      console.log('Inici chartData function');


      // var z=[];
      //
      // z.cols = [
      //   {id:0 , label:'Data', type: 'datetime'},
      //   {id:1, label:'kWh', type: 'number'}
      // ];
      // z.rows = map(this.dataModel, function(x){
      //   return [setHours(parse(x.date), x.h), x.v];
      // });

      var z = [];
      z.push(['Data', 'kWh']);
      map(this.dataModel, function(x){
        z.push([setHours(parse(x.date), x.h), x.v]);
        return;
      });
      // eslint-disable-next-line no-console
      console.log(z);

      return z;
    }

  }, //Fi de computed
  watch: {
  },
  methods: {
  } //Fi de methods()
} //export

</script>

<style/>
