<template>
  <div id="app">
    <GlobalEvents @keyup="keyMonitor"/>
    <Table/>
  </div>
</template>

<script>

import Table from './components/Table'
import GlobalEvents from 'vue-global-events'

export default {
  name: 'App',
  components: {
    GlobalEvents,
    Table
  },
  data: function () {
    return {
      sum: 0
    }
  },
  methods: {
    keyMonitor: function(event) {
      // if(event === Arrow)

      // alert(event.key)
    }
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
