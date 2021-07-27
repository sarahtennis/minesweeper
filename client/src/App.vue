<template>
  <div id="app">
    <Board />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Board from "./components/Board.vue";

@Component({
  components: {
    Board,
  },
})
export default class App extends Vue {}
</script>

<style>
*,
*:before,
*:after {
  box-sizing: border-box;
}
</style>
