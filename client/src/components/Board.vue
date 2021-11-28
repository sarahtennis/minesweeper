<template>
  <div
    id="board"
    @contextmenu="onContextMenu($event)"
    @mousedown="onMouseDown($event)"
    @mouseup="onMouseUp()"
    @mouseleave="onMouseLeave()"
  >
    <Row
      v-for="(row, index) in board"
      :key="index"
      :row="row"
      :isMouseDown="isMouseDown"
    />
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import Row from "./Row.vue";

export interface SquareData {
  row: number;
  column: number;
  isBomb: boolean;
  bombsTouching: number;
  isShowing: boolean;
  isFlagged: boolean;
  isQuestioned: boolean;
}

export interface SquareRow extends Array<SquareData> {
  [index: number]: SquareData;
}

@Component({
  components: {
    Row,
  },
})
export default class Board extends Vue {
  @Prop() public board!: SquareRow[];
  public isMouseDown = false;

  constructor() {
    super();
  }

  public onContextMenu(e: MouseEvent): void {
    e.preventDefault();
  }

  public onMouseDown(e: MouseEvent): void {
    if (e.button === 2) {
      this.isMouseDown = false;
      return;
    }
    this.isMouseDown = true;
  }

  public onMouseUp(): void {
    this.isMouseDown = false;
  }

  public onMouseLeave(): void {
    this.isMouseDown = false;
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
#board {
  display: inline-flex;
  flex-direction: column;
  align-items: flex-start;
  border: 1px solid #666;
}
</style>
