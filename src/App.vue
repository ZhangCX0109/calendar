<template>
  <div id="app">
    <img src="./assets/logo.png">
    <rj-calendar/>
  </div>
</template>

<script>
import RjCalendar from './components/RjCalendar'

export default {
  name: 'App',
  components: {
    RjCalendar
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
