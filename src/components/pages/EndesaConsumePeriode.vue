<template>
  <section class="section">
    <div class="columns">
      <div class="column is-one-third">
        <PeriodSelector
          :d-abs-initial="dAbsInitial"
          :d-abs-ends="dAbsEnds"
          @dateUpdated="dateRecived"
        />
      </div>
      <div class="column">
        <section v-if="errored">
          <p>We're sorry, we're not able to retrieve this information at the moment, please try back later</p>
        </section>
        <section v-else>
          <div>
            <PeriodSummaryKwh :data-selection="dates_selection" />
          </div>
        </section>
      </div>
    </div> <!-- Columns -->
  </section>
</template>

<script>

import PeriodSelector from '../PeriodSelector'
import PeriodSummaryKwh from '../PeriodSummaryKwh'
import axios from 'axios';
import {format, parse} from 'date-fns';

export default {
  name: 'EndesaConsumePeriode',
  components: {
    PeriodSelector,
    PeriodSummaryKwh
    },
  data: function() {
    return {
      dates_selection: [],
      loading: true,
      errored: false,
      msg_error: null,
      dAbsInitial: null,
      dAbsEnds: null
    }
  },
  mounted () {
    axios
      .get('http://dalmases.ddns.net/data/endesa',{params:{t:'s'}})
      .then(response => (
          //La date via HTPP arriba en el format YYYY/MM/DD
          this.dAbsInitial = parse(response.data[0].minAbs_date),
          this.dAbsEnds = parse(response.data[0].maxAbs_date)
        ))
      .catch(
        error => {
          this.msg_error = error,
          this.errored = true
        })
      .finally(() => this.loading = false)
    },
  methods: {
    dateRecived(arg1, arg2) {
      axios
        .get('http://dalmases.ddns.net/data/endesa',{params:{t: 'hp', d1: format(arg1,'YYYY-MM-DD'), d2: format(arg2, 'YYYY-MM-DD')}})
        .then(response => (
            this.dates_selection = response.data))
        .catch(
          error => {
            this.msg_error = error,
            this.errored = true
          })
        .finally(() => this.loading = false)
      }
    }
  }


</script>
<style/>
