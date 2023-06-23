<template>
  <div id="app">
    <PeopleDetails />
  </div>
</template>

<script>
import PeopleDetails from './components/PeopleDetails.vue'

export default {
  name: 'App',
  components: {
    PeopleDetails
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
