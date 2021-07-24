<template>
  <div id="board">
    <Row v-for="(row, index) in board" :key="index" :row="row" />
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import Row from './Row.vue';

// X determines number of columns
// Y determines number of rows
interface Dimension {
  x: number;
  y: number;
}

export interface SquareData {
  bomb: boolean;
  touching: number;
  showing: boolean;
}

@Component({
  components: {
    Row
  }
})
export default class Board extends Vue {
  public board: SquareData[][] = [
    [{bomb: false, touching: 0, showing: false}, {bomb: false, touching: 0, showing: false}, {bomb: false, touching: 0, showing: false}, {bomb: false, touching: 0, showing: false}],
    [{bomb: false, touching: 0, showing: false}, {bomb: false, touching: 0, showing: false}, {bomb: false, touching: 0, showing: false}, {bomb: false, touching: 0, showing: false}],
    [{bomb: false, touching: 0, showing: false}, {bomb: false, touching: 0, showing: false}, {bomb: false, touching: 0, showing: false}, {bomb: false, touching: 0, showing: false}],
    [{bomb: false, touching: 0, showing: false}, {bomb: false, touching: 0, showing: false}, {bomb: false, touching: 0, showing: false}, {bomb: false, touching: 0, showing: false}]
  ];

  mounted() {
    this.generateBoard();
  }

  private generateBoard() {
    const bombCount = 3;
    let bombsPlaced = 0;
    const dimension: Dimension = {
      x: 4,
      y: 4
    }

    while (bombsPlaced < bombCount) {
      const xIndex = this.getRandomInt(dimension.x);
      const yIndex = this.getRandomInt(dimension.y);

      const square = this.board[xIndex][yIndex];
      if (!square.bomb) {
        square.bomb = true;
        bombsPlaced += 1;
      }
    }
  }

  private getRandomInt(max: number) {
    return Math.floor(Math.random() * max);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
#board {
  display: flex;
  flex-direction: column;
}

.bomb {
  background: blue;
}
</style>
