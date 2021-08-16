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
      v-if="square.isFlagged && !square.isShowing"
      src="../assets/Flag.svg"
    />
    <span v-if="square.isShowing">
      {{ square.bombsTouching ? square.bombsTouching : "" }}
    </span>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
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
    if (!this.square.isShowing) this.square.isShowing = true;
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
</style>
