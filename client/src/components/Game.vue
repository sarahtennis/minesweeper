<template>
  <div id="game">
    <Header @newBoard="onNewBoard" />
    <Board :board="board" />
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import Board, { SquareRow } from "./Board.vue";
import Header from "./Header.vue";

// X determines number of rows
// Y determines number of columns
interface Dimension {
  x: number;
  y: number;
}

interface Location {
  x: number;
  y: number;
}

@Component({
  components: {
    Header,
    Board,
  },
})
export default class Game extends Vue {
  public board: SquareRow[];

  constructor() {
    super();
    this.board = this.initBoard();
  }

  public mounted() {
    this.generateBoard();
  }

  public onNewBoard() {
    this.board = this.initBoard();
    this.generateBoard();
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

  private getRandomInt(max: number) {
    return Math.floor(Math.random() * max);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
#game {
  display: inline-flex;
  flex-wrap: wrap;
}
</style>
