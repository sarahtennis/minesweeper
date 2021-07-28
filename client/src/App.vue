<template>
  <div id="app">
    <Game />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Game from "./components/Game.vue";

@Component({
  components: {
    Game,
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
