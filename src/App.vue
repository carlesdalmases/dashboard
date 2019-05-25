<template>
  <div id="app">
    <endesa />
  </div>
</template>

<script>

import endesa from './components/pages/endesa'

export default {
  name: 'App',
  components: {
    endesa
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
