<template>
  <div class="square" :class="{ 'bomb': getIsBomb() }"></div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import { SquareData } from './Board.vue';

@Component
export default class Square extends Vue {
  @Prop() private square!: SquareData;

  public getIsBomb() {
    console.log(this.square);
    return this.square.bomb;
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.square {
  height: 50px;
  width: 50px;
  background: grey;
  border: 1px solid black;
}

.bomb {
  border: 1px solid red;
}
</style>
