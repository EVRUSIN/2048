<template>
  <div id="app">

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

    }
  },
  methods: {
    // keyMonitor: function(event) {
    //   if(event.key === 'ArrowLeft') {
    //     Table.methods.moveLeft()
    //   }
    //
    //    // alert(event.key)
    // }
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
