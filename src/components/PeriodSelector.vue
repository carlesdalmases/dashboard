<template>

  <nav class="level">
    <div class="level-left">
      <div class="level-item">
        <b-field label="Selecciona data inicial">
          <b-datepicker
             placeholder="Inici"
             icon="calendar-today">
          </b-datepicker>
        </b-field>
      </div>

      <div class="level-item">
        <b-field label="Selecciona data final">
          <b-datepicker
             placeholder="Final"
             icon="calendar-today">
          </b-datepicker>
        </b-field>
      </div>
    </div>
  </nav>

</template>

<script>

export default {
  name: 'PeriodSelector'
  }

</script>

<style />
