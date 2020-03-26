<template>
  <div id="app">
    <LikelyOffers msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
  import LikelyOffers from './components/LikelyOffers.vue'

  export default {
    name: 'App',
    components: {
      LikelyOffers
    }
  }
</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
  }
</style>
