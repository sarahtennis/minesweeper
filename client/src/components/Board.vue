<template>
  <div id="board" @mousedown="onMouseDown()" @mouseup="onMouseUp()">
    <Row
      v-for="(row, index) in board"
      :key="index"
      :row="row"
      :isMouseDown="isMouseDown"
    />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Row from "./Row.vue";

// X determines number of columns
// Y determines number of rows
interface Dimension {
  x: number;
  y: number;
}

interface Location {
  x: number;
  y: number;
}

export interface SquareData {
  x: number;
  y: number;
  isBomb: boolean;
  bombsTouching: number;
  isShowing: boolean;
  isFlagged: boolean;
}

interface SquareRow extends Array<SquareData> {
  [index: number]: SquareData;
}

@Component({
  components: {
    Row,
  },
})
export default class Board extends Vue {
  public board: SquareRow[];
  public isMouseDown = false;

  constructor() {
    super();
    this.board = this.initBoard();
  }

  mounted(): void {
    this.generateBoard();
  }

  public onMouseDown(): void {
    this.isMouseDown = true;
  }

  public onMouseUp(): void {
    this.isMouseDown = false;
  }

  private initBoard(): SquareRow[] {
    let outputArray = [];
    const xIndex = 4;
    const yIndex = 4;
    for (let y = 0; y < yIndex; y++) {
      const addArray = new Array(xIndex).fill("").map(() => {
        return {
          x: 0,
          y: 0,
          isBomb: false,
          bombsTouching: 0,
          isShowing: false,
          isFlagged: false,
        };
      });
      outputArray.push(addArray);
    }
    return outputArray;
  }

  private generateBoard(): void {
    const bombLocations = this.placeBombs();
    this.incrementAroundBombs(bombLocations);
  }

  private incrementAroundBombs(bombLocations: Location[]): void {
    bombLocations.forEach((location: Location) => {
      const { x, y } = location;
      const isTopRow = x === 0;
      const isBottomRow = this.board.length === x + 1;
      const isLeftColumn = y === 0;
      const isRightColumn = this.board[0].length === y + 1;

      if (!isTopRow) this.incrementAbove(location, isLeftColumn, isRightColumn);
      this.incrementSides(location, isLeftColumn, isRightColumn);
      if (!isBottomRow)
        this.incrementBelow(location, isLeftColumn, isRightColumn);
    });
  }

  private incrementBelow(
    location: Location,
    isLeftColumn: boolean,
    isRightColumn: boolean
  ): void {
    if (!isLeftColumn) {
      this.board[location.x + 1][location.y - 1].bombsTouching += 1;
    }
    this.board[location.x + 1][location.y].bombsTouching += 1;
    if (!isRightColumn) {
      this.board[location.x + 1][location.y + 1].bombsTouching += 1;
    }
  }

  private incrementSides(
    location: Location,
    isLeftColumn: boolean,
    isRightColumn: boolean
  ): void {
    if (!isLeftColumn) {
      this.board[location.x][location.y - 1].bombsTouching += 1;
    }
    if (!isRightColumn) {
      this.board[location.x][location.y + 1].bombsTouching += 1;
    }
  }

  private incrementAbove(
    location: Location,
    isLeftColumn: boolean,
    isRightColumn: boolean
  ): void {
    if (!isLeftColumn) {
      this.board[location.x - 1][location.y - 1].bombsTouching += 1;
    }
    this.board[location.x - 1][location.y].bombsTouching += 1;
    if (!isRightColumn) {
      this.board[location.x - 1][location.y + 1].bombsTouching += 1;
    }
  }

  private placeBombs(): Location[] {
    const bombCount = 3;
    let bombsPlaced = 0;
    const bombLocations = [];
    const dimension: Dimension = {
      x: 4,
      y: 4,
    };

    while (bombsPlaced < bombCount) {
      const xIndex = this.getRandomInt(dimension.x);
      const yIndex = this.getRandomInt(dimension.y);

      const square = this.board[xIndex][yIndex];
      if (!square.isBomb) {
        square.isBomb = true;
        bombsPlaced++;
        bombLocations.push({ x: xIndex, y: yIndex });
      }
    }

    return bombLocations;
  }

  private getRandomInt(max: number) {
    return Math.floor(Math.random() * max);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
#board {
  display: inline-flex;
  flex-direction: column;
  border: 1px solid #666;
}
</style>
