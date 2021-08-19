<template>
  <div
    class="square"
    :class="{
      bomb: getIsBomb(),
      hovered: getIsHovered(),
      showing: getIsShowing(),
      flagged: getIsFlagged(),
    }"
    @contextmenu="onRightClick($event)"
    @mousedown="onMouseDown($event)"
    @mouseup="onMouseUp($event)"
    @mouseleave="onMouseLeave($event)"
    @mouseenter="onMouseEnter($event)"
  >
    <img
      class="square-showing"
      v-if="square.isFlagged && !square.isShowing"
      src="../assets/FlagPixel.svg"
    />
    <div class="square-showing" v-if="square.isShowing">
      <img
        class="bomb-count-number"
        v-if="square.bombsTouching === 1"
        src="../assets/One.svg"
      />
      <span v-if="square.bombsTouching !== 1">{{
        square.bombsTouching ? square.bombsTouching : ""
      }}</span>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import EventBus from "../event-bus";
import { SquareData } from "./Board.vue";

@Component
export default class Square extends Vue {
  @Prop() private square!: SquareData;
  @Prop() private isMouseDown!: boolean;
  private isHovered = false;

  public onMouseUp(e: MouseEvent): void {
    if (e.button === 2 || this.getIsFlagged()) {
      e.preventDefault();
      return;
    }
    this.isHovered = false;
    EventBus.$emit("squareClicked", this.square);
  }

  public onMouseLeave(): void {
    this.isHovered = false;
  }

  public onMouseEnter(): void {
    if (this.isMouseDown) {
      this.isHovered = true;
    } else {
      this.isHovered = false;
    }
  }

  public onMouseDown(e: MouseEvent): void {
    if (e.button === 2 || this.getIsFlagged()) {
      e.preventDefault();
      return;
    }
    this.isHovered = true;
  }

  public onRightClick(e: MouseEvent): void {
    // TODO: add ? icon functionality
    if (!this.square.isShowing) {
      this.square.isFlagged = !this.square.isFlagged;
    }
    e.preventDefault();
  }

  public getIsHovered(): boolean {
    return this.isHovered;
  }

  public getIsBomb(): boolean {
    return this.square.isShowing && this.square.isBomb;
  }

  public getIsShowing(): boolean {
    return this.square.isShowing;
  }

  public getIsFlagged(): boolean {
    return this.square.isFlagged;
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.square {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 50px;
  width: 50px;
  background: #888;
  border-width: 8px;
  border-style: solid;
  border-top-color: #ccc;
  border-left-color: #ccc;
  border-right-color: #666;
  border-bottom-color: #666;
  user-select: none;
}

.square.hovered,
.square.showing {
  border: 2px solid #666;
}

.bomb.showing {
  border: 1px solid red;
}

.square-showing {
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.bomb-count-number {
  height: 50%;
  width: auto;
}
</style>
