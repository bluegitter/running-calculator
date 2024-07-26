<template>
  <div id="app">
    <running-calculator />
  </div>
</template>

<script>
import RunningCalculator from './components/RunningCalculator.vue'

export default {
  name: 'App',
  components: {
    RunningCalculator
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
