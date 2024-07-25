<template>
  <div id="app">
    <home-vue></home-vue>
  </div>
</template>

<script>
import HomeVue from './views/HomeVue/index.vue'

export default {
  name: 'App',
  components: {
    HomeVue
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
