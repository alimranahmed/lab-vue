<template>
  <div id="app">
<!--    <LikelyOffers msg="Welcome to Your Vue.js App"/>-->
    <pusher-client></pusher-client>
  </div>
</template>

<script>
  // import LikelyOffers from './components/LikelyOffers.vue'

  import PusherClient from "./components/PusherClient";

  export default {
    name: 'App',
    components: {
      PusherClient,
      // LikelyOffers
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
