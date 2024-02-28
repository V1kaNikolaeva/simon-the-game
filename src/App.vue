<template>
  <div id="app">
    <div class="wrapper">
      <SimonGame></SimonGame>

    </div>
  </div>
</template>

<script>
import SimonGame from './components/SimonGame.vue';

export default {
  name: 'App',

  components: { SimonGame }
}
</script>

<style scoped>
.wrapper {
    margin: 0 auto;
    padding-top: 30px;
    max-width: 500px;
    height: 100%;
}
</style>
