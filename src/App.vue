<template>
  <div id="app">
    <!-- <router-view/> -->
    <calendar weeksType="CN" @dateChoose="getDate" defaultDate="Thu Sep 06 2018 00:00:00 GMT+0800 (中国标准时间)">
    </calendar>
  </div>
</template>

<script>
import calendar from './calendar'
export default {
  name: 'App',
  components: {
    calendar
  },
  data () {
    return {}
  },
  methods: {
    getDate (item) {
      console.log(item)
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
