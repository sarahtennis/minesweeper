<template>
  <div id="app">
    <Game />
    <DigitalNumber :number="timerNumber" />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Game from "./components/Game.vue";
import DigitalNumber, { Numbers } from "./components/DigitalNumber.vue";

@Component({
  components: {
    Game,
    DigitalNumber,
  },
})
export default class App extends Vue {
  public timer = 0;
  private interval = 0;

  mounted() {
    this.interval = setInterval(() => {
      this.timer++;
    }, 1000);
  }

  unmounted() {
    clearInterval(this.interval);
  }

  get timerNumber(): boolean[] {
    const timerNumber = this.timer.toString();
    if (timerNumber.length < 1) {
      return Numbers.ZERO;
    }
    switch (timerNumber[timerNumber.length - 1]) {
      case "0":
        return Numbers.ZERO;
      case "1":
        return Numbers.ONE;
      case "2":
        return Numbers.TWO;
      case "3":
        return Numbers.THREE;
      case "4":
        return Numbers.FOUR;
      case "5":
        return Numbers.FIVE;
      case "6":
        return Numbers.SIX;
      case "7":
        return Numbers.SEVEN;
      case "8":
        return Numbers.EIGHT;
      case "9":
        return Numbers.NINE;
      default:
        return Numbers.EMPTY;
    }
  }
}
</script>

<style>
*,
*:before,
*:after {
  box-sizing: border-box;
}
</style>
