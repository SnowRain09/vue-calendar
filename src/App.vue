<template>
  <div id="app">
    <!-- <router-view/> -->
    <calendar weeksType="EN" @dateChoose="getDate">
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
