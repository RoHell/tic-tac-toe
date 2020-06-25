<template>
  <div id="app">
    <TicTacToe />
  </div>
</template>

<script>
import TicTacToe from "./components/TicTacToe.vue";
export default {
  components: {
    TicTacToe
  }
};
</script>

<style lang="scss">
#app {
  font-family: monospace;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}
</style>
