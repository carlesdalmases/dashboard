<template>
  <section>
    <h1 class="title is-4">Consum per tarifes, kWh</h1>
    <b-table
      :data="consumeByRates"
      :striped="true"
      detailed
      detail-key="rate"
      show-detail-icon
    >
      <template slot-scope="props">
        <b-table-column field="rate" label="Tarifa" width="40">
          <a @click="toggle(props.row)">
            {{ props.row.rate }}
          </a>
        </b-table-column>

        <b-table-column field="p1" label="P1 - Pic" numeric>
          {{ props.row.p1 | ifExist | truncate_kwh }}
          <b-tag v-if="props.row.p1" type="is-danger">
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

        <b-table-column field="ce" label="Cotxe elèctric" numeric>
          {{ props.row.ce | ifExist | truncate_kwh  }}
          <b-tag v-if="props.row.ce" type="is-success">
            {{ props.row.ce | ifExist | perCent(consumeByRates)}}
          </b-tag>

        </b-table-column>

      </template>
      <template slot="detail" slot-scope="props">
        <article class="media">
          <div class="media-content">
            <div class="content">
              <p>
                <strong>{{ props.row.rate }}</strong>
                <br>
                {{ props.row.detail }}
              </p>
            </div>
          </div>
        </article>
      </template>
    </b-table>
  </section>
</template>

<script>

import { remove, includes, groupBy, forEach, find} from 'lodash';
import { parse } from 'date-fns';

export default {
  name: 'PeriodSummaryKwh',
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
      consumeByRates: []
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

      const ce = [1,2,3,4,5,6,7];

      //Objectes
      // Tarifa sense discriminació horària
      let obj20A = {
        rate:"20A",
        p1:0,
        detail: "Sense discriminació horària"
      };
      // Tarifa amb discriminació horària (P1-Pic i P2-Vall), amb horari d'estiu i d'hivern
      let obj20DHA = {
        rate:"20DHA",
        p1:0,
        p2:0,
        detail: "Amb discriminació horària. En horari d'hivern, P1: de les 12h a les 22h, P2: de les 22h a les 12h. En horari d'estiu, P1: de les 13h a les 23h, P2: de les 23h a les 13h."
      };
      //Tarifa cotxe elèctric amb discriminació horària (P1-Pic, P2-Vall, P3-Supervall)
      let obj20DHS = {
        rate:"20DHS",
        p1:0,
        p2:0,
        p3:0,
        detail: "Amb discriminació horària. P1: de les 13h a les 23h, P2: de les 23h a la 1h i de les 7h a les 13h. P3: de les 1h a les 7h."
      };

      //Consum del cotxe elèctric, en kWh, entre les 00:00 i les 07:00h
      let objCE = {
        rate:"CE",
        ce:0,
        detail: "Consum entre les 00:00h i les 07:00h, típicament quan es carrega el cotxe elèctric."
      };

      //groupBy day
      forEach(groupBy(data, function(o){return o.date}), function(v){

        if((parse(v[0].date.split('T')[0])).getTimezoneOffset() == offsetWinter20DHA){
          //Winter
          forEach(v, function(x){

            obj20A.p1 += x.v;

            if(includes(ce, x.h)){
              objCE.ce += x.v;
            }

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

            if(includes(ce, x.h)){
              objCE.ce += x.v;
            }

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
      x.push(objCE);
      return x;
    },
    toggle(row) {
      this.$refs.table.toggleDetails(row)
    }
  } //methods
}

</script>

<style />
