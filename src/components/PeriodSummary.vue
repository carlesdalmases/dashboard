<template>
  <section>
    <b-table
      :data="consumeByRates"
      :striped="true"
    >
      <template slot-scope="props">
        <b-table-column field="rate" label="Tarifa" width="40">
          {{ props.row.rate }}
        </b-table-column>

        <b-table-column field="p1" label="P1 - Pic" numeric>
          {{ props.row.p1 | ifExist | truncate_kwh }}
          <b-tag type="is-danger">
            {{ props.row.p1 | ifExist | perCent(consumeByRates) }}
          </b-tag>
        </b-table-column>

        <b-table-column field="p2" label="P2 - Vall" numeric>
          {{ props.row.p2 | ifExist | truncate_kwh }}
          <b-tag v-if="props.row.p2" type="is-warning">
            {{ props.row.p2 | ifExist | perCent(consumeByRates) }}
          </b-tag>
        </b-table-column>

        <b-table-column field="p3" label="P3 - SuperVall" numeric>
          {{ props.row.p3 | ifExist | truncate_kwh  }}
          <b-tag v-if="props.row.p3" type="is-success">
            {{ props.row.p3 | ifExist | perCent(consumeByRates)}}
          </b-tag>
        </b-table-column>
      </template>
    </b-table>
  </section>
</template>

<script>

import { remove, includes, groupBy, forEach, find} from 'lodash';
import { parse } from 'date-fns';

export default {
  name: 'PeriodSummary',
  filters: {
    truncate_kwh: function (value) {
      if (value) {
        return Math.trunc(value);
      }
      else {
        return;
      }
    },
    ifExist: function (value) {
      if(value) {
        return value;
      }
      else {
        return null;
      }
    },
    perCent: function (value, arg1) {
      if(value) {
        return Math.round(((value)/((find(arg1, { 'rate': '20A' })).p1))*100) + '%';
      }
      else {
        return;
      }
    }
  },
  props: {
    dataSelection: {
      type: Array,
      default() {
        return [];
      }
    }
  },
  data: function(){
    return {
      consumeByRates: [],
      columns: [
        {
          field: 'rate',
          label: 'Tarifes',
          width: '40',
          numeric: false
        },
        {
          field: 'p1',
          label: 'P1 - Pic',
          width: '40',
          numeric: true
        },
        {
          field: 'p2',
          label: 'P2 - Vall',
          width: '40',
          numeric: true
        },
        {
          field: 'p3',
          label: 'P3 - SuperVall',
          width: '40',
          numeric: true
        }
      ]
      }
  },
  watch: {
    dataSelection: function () {
      remove(this.consumeByRates, function(){return true;});
      this.consumeByRates = this.calcConsumeByRates(this.dataSelection);
      return;
    }
  },
  methods: {
    calcConsumeByRates: function(data) {
      //Array amb els tres objectes amb els totals per tarifes
      let x =[];

      const offsetWinter20DHA = -60;
      const p1Winter20DHA = [13,14,15,16,17,18,19,20,21,22];
      const p1Summer20DHA = [14,15,16,17,18,19,20,21,22,23];
      // const p2Winter20DHA = [1,2,3,4,5,6,7,8,9,10,11,12,23,24];
      // const p2Summer20DHA = [1,2,3,4,5,6,7,8,9,10,11,12,13,24];

      const p120DHS = [14,15,16,17,18,19,20,21,22,23];
      const p220DHS = [1,8,9,10,11,12,13,24];
      // const p320DHS = [2,3,4,5,6,7];

      //Objectes
      // Tarifa sense discriminació horària
      let obj20A = {
        rate:"20A",
        p1:0
      };
      // Tarifa amb discriminació horària (P1-Pic i P2-Vall), amb horari d'estiu i d'hivern
      let obj20DHA = {
        rate:"20DHA",
        p1:0,
        p2:0
      };
      //Tarifa cotxe elèctric amb discriminació horària (P1-Pic, P2-Vall, P3-Supervall)
      let obj20DHS = {
        rate:"20DHS",
        p1:0,
        p2:0,
        p3:0
      };

      //groupBy day
      forEach(groupBy(data, function(o){return o.date}), function(v){

        if((parse(v[0].date.split('T')[0])).getTimezoneOffset() == offsetWinter20DHA){
          //Winter
          forEach(v, function(x){

            obj20A.p1 += x.v;

            if(includes(p1Winter20DHA, x.h)){
              obj20DHA.p1 += x.v;
            } else {
              obj20DHA.p2 += x.v;
            }

            if(includes(p120DHS, x.h)) {
              obj20DHS.p1 += x.v;
            } else if(includes(p220DHS, x.h)) {
              obj20DHS.p2 += x.v;
            } else {
              obj20DHS.p3 += x.v;
            }
          }) //forEach
        } else {
          //Summer
          forEach(v, function(x){

            obj20A.p1 += x.v;

            if(includes(p1Summer20DHA, x.h)){
              obj20DHA.p1 += x.v;
            } else {
              obj20DHA.p2 += x.v;
            }

            if(includes(p120DHS, x.h)) {
              obj20DHS.p1 += x.v;
            } else if(includes(p220DHS, x.h)) {
              obj20DHS.p2 += x.v;
            } else {
              obj20DHS.p3 += x.v;
            }
          });
        } //getTimeOffset
      });  //forEach groupBy
      x.push(obj20A);
      x.push(obj20DHA);
      x.push(obj20DHS);
      return x;
    }
  } //methods
}

</script>

<style />
