<template>
  <div id="app">
    <Teams/>
  </div>
</template>

<script>
import Teams from './components/Teams'

export default {
  name: 'App',
  components: {
    Teams
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
