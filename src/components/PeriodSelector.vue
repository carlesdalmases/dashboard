<template>
  <section class="section text_left">
    <section class="container container_margins">
      <b-field label="Data inicial">
        <b-datepicker
          v-model="dateInitial"
          placeholder="Inici"
          icon="calendar-today"
          icon-pack="mdi"
          :first-day-of-week="firstdayofweek"
          :day-names="daynames"
          :month-names="monthnames"
          :min-date="dAbsInitial"
          :max-date="dAbsEnds"
          :focused-date="dAbsEnds"
          @input="checkperiode"
        />
      </b-field>
    </section>
    <section class="container container_margins">
      <b-field label="Data final">
        <b-datepicker
          v-model="dateEnds"
          placeholder="Final"
          icon="calendar-today"
          icon-pack="mdi"
          :first-day-of-week="firstdayofweek"
          :day-names="daynames"
          :month-names="monthnames"
          :min-date="dateInitial"
          :max-date="dAbsEnds"
          :focused-date="dateInitial"
        />
      </b-field>
    </section>
    <section class="container container_margins text_right">
      <b-button @click="sendDateToParent">
        Seleccionar
      </b-button>
    </section>
  </section>
</template>

<script>

import { cloneDeep } from 'lodash';

export default {
  name: 'PeriodSelector',
  props: {
    dAbsInitial: {
      type: Date,
      default: null
    },
    dAbsEnds: {
      type: Date,
      default: null
    }
  },
  data: function(){
    return {
      firstdayofweek: 1,
      daynames: ["Dg", "Dll", "Dm", "Dx", "Dj", "Dv", "Ds"],
      monthnames: ["Gener", "Febrer", "Març", "Abril", "Maig", "Juny", "Juliol", "Agost", "Setembre", "Octubre", "Novembre", "Desembre"],
      dateInitial: cloneDeep(this.dAbsEnds),
      dateEnds: cloneDeep(this.dAbsEnds),
      disabledCalendarEnds: true
      }
    },
  watch: {
    dAbsInitial: function () {
      this.dateInitial= this.dAbsInitial
    },
    dAbsEnds: function () {
      this.dateEnds= this.dAbsEnds
    }
  },
  methods: {
      sendDateToParent: function () {
        if (this.dateInitial && this.dateEnds) {
          return this.$emit("dateUpdated", this.dateInitial, this.dateEnds);
        }
      },
      checkperiode: function () {
        if (this.dateEnds){
          if (this.dateInitial.getTime() > this.dateEnds.getTime()) {
            this.dateInitial = new Date(this.dateEnds.getTime());
          }
        }
      }
    }
  }

</script>

<style scoped>
.text_left {
  text-align: left;
}
.text_right {
  text-align: right;
}
.container_margins {
  margin-top: 10px;
  margin-bottom: 10px;
}
</style>
