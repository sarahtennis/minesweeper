<template>
  <div id="game">
    <Dimensions
      :rows="dimensions.rows"
      :columns="dimensions.rows"
      @updateDimensions="onUpdateDimensions"
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

interface Dimension {
  rows: number;
  columns: number;
}

interface Location {
  row: number;
  column: number;
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
    rows: 10,
    columns: 10,
  };
  public bombCount = 20;

  constructor() {
    super();
    this.board = this.initBoard(this.dimensions.rows, this.dimensions.columns);
  }

  public mounted(): void {
    this.generateBoard();
    EventBus.$on("squareClicked", (square: SquareData) => {
      this.onSquareClicked(square);
    });
  }

  public updateRows(value: string): void {
    const newRows = parseInt(value);
    console.log(value);
    if (newRows > 50) {
      this.dimensions.rows = 50;
    } else if (newRows < 1) {
      this.dimensions.rows = 1;
    } else {
      this.dimensions.rows = newRows;
    }
  }

  public updateColumns(value: string): void {
    this.dimensions.columns = parseInt(value);
  }

  public onUpdateDimensions({ rows, columns }: Dimension): void {
    this.dimensions.rows = rows;
    this.dimensions.columns = columns;

    const newMaxBombs = this.dimensions.rows * this.dimensions.columns;
    if (this.bombCount > newMaxBombs) {
      this.bombCount = newMaxBombs;
    }

    this.onNewBoard();
  }

  private onSquareClicked(square: SquareData) {
    const { row, column } = square;
    if (!square.isFlagged && !square.isQuestioned && !square.isShowing) {
      this.board[row][column].isShowing = true;
      if (square.isBomb) {
        // TODO: loss logic/visuals
      } else if (square.bombsTouching === 0) {
        this.clickNeighbors({ row, column });
      }
    }
  }

  private clickBelow(
    location: Location,
    isLeftColumn: boolean,
    isRightColumn: boolean
  ): void {
    if (!isLeftColumn) {
      this.clickSquare(this.board[location.row + 1][location.column - 1]);
    }
    this.clickSquare(this.board[location.row + 1][location.column]);
    if (!isRightColumn) {
      this.clickSquare(this.board[location.row + 1][location.column + 1]);
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
    const left = this.board[location.row][location.column - 1];
    const right = this.board[location.row][location.column + 1];
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
      this.clickSquare(this.board[location.row - 1][location.column - 1]);
    }
    this.clickSquare(this.board[location.row - 1][location.column]);
    if (!isRightColumn) {
      this.clickSquare(this.board[location.row - 1][location.column + 1]);
    }
  }

  private clickNeighbors(location: Location) {
    const { row, column } = location;
    const isTopRow = row === 0;
    const isBottomRow = this.board.length === row + 1;
    const isLeftColumn = column === 0;
    const isRightColumn = this.board[0].length === column + 1;

    if (!isTopRow) this.clickAbove(location, isLeftColumn, isRightColumn);
    this.clickSides(location, isLeftColumn, isRightColumn);
    if (!isBottomRow) this.clickBelow(location, isLeftColumn, isRightColumn);
  }

  public onNewBoard(): void {
    this.board = this.initBoard(this.dimensions.rows, this.dimensions.columns);
    this.generateBoard();
  }

  private initBoard(rows: number, columns: number): SquareRow[] {
    let outputArray = [];
    for (let r = 0; r < rows; r++) {
      const addArray = [];
      for (let c = 0; c < columns; c++) {
        const squareData = {
          row: r,
          column: c,
          isBomb: false,
          bombsTouching: 0,
          isShowing: false,
          isFlagged: false,
          isQuestioned: false,
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
      const row = this.getRandomInt(this.dimensions.rows);
      const column = this.getRandomInt(this.dimensions.rows);

      const square = this.board[row][column];
      if (!square.isBomb) {
        square.isBomb = true;
        bombsPlaced++;
        bombLocations.push({ row, column });
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
      const { row, column } = location;
      const isTopRow = row === 0;
      const isBottomRow = this.board.length === row + 1;
      const isLeftColumn = column === 0;
      const isRightColumn = this.board[0].length === column + 1;

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
      this.board[location.row + 1][location.column - 1].bombsTouching += 1;
    }
    this.board[location.row + 1][location.column].bombsTouching += 1;
    if (!isRightColumn) {
      this.board[location.row + 1][location.column + 1].bombsTouching += 1;
    }
  }

  private incrementSides(
    location: Location,
    isLeftColumn: boolean,
    isRightColumn: boolean
  ): void {
    if (!isLeftColumn) {
      this.board[location.row][location.column - 1].bombsTouching += 1;
    }
    if (!isRightColumn) {
      this.board[location.row][location.column + 1].bombsTouching += 1;
    }
  }

  private incrementAbove(
    location: Location,
    isLeftColumn: boolean,
    isRightColumn: boolean
  ): void {
    if (!isLeftColumn) {
      this.board[location.row - 1][location.column - 1].bombsTouching += 1;
    }
    this.board[location.row - 1][location.column].bombsTouching += 1;
    if (!isRightColumn) {
      this.board[location.row - 1][location.column + 1].bombsTouching += 1;
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
