<template>
  <div id="app">
    <Weather />
  </div>
</template>

<script>
import Weather from './components/Weather.vue';

export default {
  components: {
    Weather,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #0077ff;
}
</style>
