<template>
  <div>
    <AppHeader />
    <EndesaConsumePeriode />
    <AppFooter />
  </div>
</template>

<script>

import AppHeader from '../AppHeader'
import AppFooter from '../AppFooter'
import EndesaConsumePeriode from './EndesaConsumePeriode'

export default {
  name: 'Endesa',
  components: {
    AppHeader, AppFooter, EndesaConsumePeriode
    }
  }

</script>

<style/>
