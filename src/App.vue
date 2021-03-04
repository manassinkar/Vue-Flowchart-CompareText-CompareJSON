<template>
  <div id="app">
    <flowchart-compare/>
  </div>
</template>

<script>
import FlowchartCompare from './components/FlowchartCompare.vue'

export default {
  name: 'App',
  components: {
    FlowchartCompare
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
