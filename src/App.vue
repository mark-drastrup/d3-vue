<template>
  <div id="app">
    <Chart />
  </div>
</template>

<script>
import Chart from "./components/Chart.vue"

export default {
  name: 'app',
  components: {
    Chart
  }
}
</script>

<style>
body {
   background-color: #293740;
   padding: 0;
   margin: 0;
   
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
