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
    <div class="square-showing">
      <!-- <img class="square-icon" :src="getIconSrc()" /> -->
      <component
        class="square-icon"
        v-if="square.isShowing"
        v-bind:is="getIconComponent()"
      ></component>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import EventBus from "../event-bus";
import { SquareData } from "./Board.vue";
import Bomb from "../assets/Bomb.vue";
import One from "../assets/One.vue";
import Two from "../assets/Two.vue";
import Three from "../assets/Three.vue";
import Four from "../assets/Four.vue";
import Five from "../assets/Five.vue";
import Six from "../assets/Six.vue";
import Seven from "../assets/Seven.vue";
import Eight from "../assets/Eight.vue";

const BombCountToSvgName: { [index: number]: string } = {
  1: "One",
  2: "Two",
  3: "Three",
  4: "Four",
  5: "Five",
  6: "Six",
  7: "Seven",
  8: "Eight",
};

@Component({
  components: {
    Bomb,
    One,
    Two,
    Three,
    Four,
    Five,
    Six,
    Seven,
    Eight,
  },
})
export default class Square extends Vue {
  @Prop() private square!: SquareData;
  @Prop() private isMouseDown!: boolean;
  private isHovered = false;

  public getIconComponent(): string {
    if (this.square.isBomb) {
      return "Bomb";
    }
    return BombCountToSvgName[this.square.bombsTouching];
  }

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
  background: #c0c0c0;
  border-width: 8px;
  border-style: solid;
  border-top-color: #fff;
  border-left-color: #fff;
  border-right-color: #808080;
  border-bottom-color: #808080;
  user-select: none;
}

.square.hovered,
.square.showing {
  border: 2px solid #808080;
}

.square-showing {
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.bomb-count-number,
.square-icon {
  height: 50%;
  width: auto;
}
</style>
