<template>
  <CardCreator/>
</template>

<script>
import CardCreator from './components/CardCreator.vue'

export default {
  name: 'App',
  components: {
    CardCreator
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
}
</style>
