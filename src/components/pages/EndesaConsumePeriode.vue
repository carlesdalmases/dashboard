<template>
  <div>
    <PeriodSelector
      :d-abs-initial="dAbsInitial"
      :d-abs-ends="dAbsEnds"
      @dateUpdated="dateRecived"
    />
    <section v-if="errored">
      <p>We're sorry, we're not able to retrieve this information at the moment, please try back later</p>
    </section>
    <section v-else>
      <div v-if="loading">
        Loading...
      </div>
      <div v-else>
        <!-- <p>Dades HTTP {{ dAbsInitial }} {{ dAbsEnds }}</p> -->
        <!-- <b-table
          :data="dates_selection"
          :columns="columns"
        /> -->
        <PeriodSummary :data-selection="dates_selection" />
      </div>
    </section>
  </div>
</template>

<script>

import PeriodSelector from '../PeriodSelector'
import PeriodSummary from '../PeriodSummary'
import axios from 'axios';
import {format, parse} from 'date-fns';
// import {keys} from 'lodash';

export default {
  name: 'EndesaConsumePeriode',
  components: {
    PeriodSelector,
    PeriodSummary
    },
  data: function() {
    return {
      dates_selection: [],
      columns: [
        {
          field: 'date',
          label: 'Data'
      },
      {
        field: 'h',
        label: 'Hora'
      },
      {
        field: 'v',
        label: 'kw/h'
      }
      ],
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
