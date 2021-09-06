<template>
  <div id="game">
    <Dimensions
      :newDimensions="onNewDimensions"
      :rows="dimensions.x"
      :columns="dimensions.y"
      :updateColumns="updateColumns"
      :updateRows="updateRows"
    />
    <Header @newBoard="onNewBoard" />
    <Board :board="board" />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Board, { SquareRow, SquareData } from "./Board.vue";
import Dimensions from "./Dimensions.vue";
import Header from "./Header.vue";
import EventBus from "../event-bus";

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
    Dimensions,
    Header,
    Board,
  },
})
export default class Game extends Vue {
  public board: SquareRow[];
  public dimensions: Dimension = {
    x: 10,
    y: 10,
  };
  public bombCount = 20;

  constructor() {
    super();
    this.board = this.initBoard(this.dimensions.x, this.dimensions.y);
  }

  public mounted(): void {
    this.generateBoard();
    EventBus.$on("squareClicked", (square: SquareData) => {
      this.onSquareClicked(square);
    });
  }

  public updateRows(value: string): void {
    this.dimensions.x = parseInt(value);
  }

  public updateColumns(value: string): void {
    this.dimensions.y = parseInt(value);
  }

  public onNewDimensions(): void {
    const newMaxBombs = this.dimensions.x * this.dimensions.y;
    if (this.bombCount > newMaxBombs) {
      this.bombCount = newMaxBombs;
    }
    this.onNewBoard();
  }

  private onSquareClicked(square: SquareData) {
    const { x, y } = square;
    if (!square.isFlagged && !square.isShowing) {
      this.board[x][y].isShowing = true;
      if (square.isBomb) {
        // TODO: loss logic/visuals
      } else if (square.bombsTouching === 0) {
        this.clickNeighbors({ x, y });
      }
    }
  }

  private clickBelow(
    location: Location,
    isLeftColumn: boolean,
    isRightColumn: boolean
  ): void {
    if (!isLeftColumn) {
      this.clickSquare(this.board[location.x + 1][location.y - 1]);
    }
    this.clickSquare(this.board[location.x + 1][location.y]);
    if (!isRightColumn) {
      this.clickSquare(this.board[location.x + 1][location.y + 1]);
    }
  }

  private clickSquare(square: SquareData) {
    this.onSquareClicked(square);
  }

  private clickSides(
    location: Location,
    isLeftColumn: boolean,
    isRightColumn: boolean
  ): void {
    const left = this.board[location.x][location.y - 1];
    const right = this.board[location.x][location.y + 1];
    if (!isLeftColumn) {
      this.clickSquare(left);
    }
    if (!isRightColumn) {
      this.clickSquare(right);
    }
  }

  private clickAbove(
    location: Location,
    isLeftColumn: boolean,
    isRightColumn: boolean
  ): void {
    if (!isLeftColumn) {
      this.clickSquare(this.board[location.x - 1][location.y - 1]);
    }
    this.clickSquare(this.board[location.x - 1][location.y]);
    if (!isRightColumn) {
      this.clickSquare(this.board[location.x - 1][location.y + 1]);
    }
  }

  private clickNeighbors(location: Location) {
    const { x, y } = location;
    const isTopRow = x === 0;
    const isBottomRow = this.board.length === x + 1;
    const isLeftColumn = y === 0;
    const isRightColumn = this.board[0].length === y + 1;

    if (!isTopRow) this.clickAbove(location, isLeftColumn, isRightColumn);
    this.clickSides(location, isLeftColumn, isRightColumn);
    if (!isBottomRow) this.clickBelow(location, isLeftColumn, isRightColumn);
  }

  public onNewBoard(): void {
    this.board = this.initBoard(this.dimensions.x, this.dimensions.y);
    this.generateBoard();
  }

  private initBoard(x: number, y: number): SquareRow[] {
    let outputArray = [];
    const xIndex = x;
    const yIndex = y;
    for (let y = 0; y < yIndex; y++) {
      const addArray = [];
      for (let x = 0; x < xIndex; x++) {
        const squareData = {
          x: y,
          y: x,
          isBomb: false,
          bombsTouching: 0,
          isShowing: false,
          isFlagged: false,
        };
        addArray.push(squareData);
      }
      outputArray.push(addArray);
    }
    return outputArray;
  }

  private placeBombs(): Location[] {
    const bombCount = this.bombCount;
    let bombsPlaced = 0;
    const bombLocations = [];

    while (bombsPlaced < bombCount) {
      const xIndex = this.getRandomInt(this.dimensions.x);
      const yIndex = this.getRandomInt(this.dimensions.y);

      const square = this.board[yIndex][xIndex];
      if (!square.isBomb) {
        square.isBomb = true;
        bombsPlaced++;
        bombLocations.push({ x: yIndex, y: xIndex });
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
