<template>
  <div
    class="square"
    :class="{
      pressed: getIsPressed(),
      showing: getIsShowing(),
    }"
    @mouseup="onMouseUp($event)"
    @mouseleave="onMouseLeave($event)"
    @mouseenter="onMouseEnter($event)"
  >
    <!-- Numbers and bomb -->
    <component
      class="square-icon"
      v-if="square.isShowing"
      v-bind:is="getIconComponent()"
    ></component>
    <!-- Flag -->
    <component
      class="square-icon"
      v-if="(square.isFlagged || square.isQuestioned) && !square.isShowing"
      v-bind:is="getIconComponent()"
    ></component>
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
import Flag from "../assets/Flag.vue";
import QuestionMark from "../assets/QuestionMark.vue";

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
    Flag,
    QuestionMark,
  },
})
export default class Square extends Vue {
  @Prop() private square!: SquareData;
  @Prop() private isMouseDown!: boolean;
  private isHovered = false;

  public getIconComponent(): string {
    if (this.square.isFlagged) {
      return "Flag";
    }
    if (this.square.isQuestioned) {
      return "QuestionMark";
    }
    if (this.square.isBomb) {
      return "Bomb";
    }
    return BombCountToSvgName[this.square.bombsTouching];
  }

  public onMouseUp(e: MouseEvent): void {
    // Mouse up from external mouse down
    if (this.getIsShowing()) {
      return;
    }
    // Right click
    if (e.button === 2) {
      this.handleRightClick();
      return;
    }
    // left click on flagged or questioned
    if (!this.isMouseDown || this.getIsFlagged() || this.getIsQuestioned()) {
      return;
    }
    EventBus.$emit("squareClicked", this.square);
  }

  public onMouseLeave(): void {
    this.isHovered = false;
  }

  public onMouseEnter(): void {
    this.isHovered = true;
  }

  private handleRightClick(): void {
    if (!this.getIsShowing()) {
      if (this.getIsFlagged()) {
        this.square.isFlagged = false;
        this.square.isQuestioned = true;
      } else if (this.getIsQuestioned()) {
        this.square.isFlagged = false;
        this.square.isQuestioned = false;
      } else {
        this.square.isFlagged = true;
        this.square.isQuestioned = false;
      }
    }
  }

  public getIsPressed(): boolean {
    return (
      !this.getIsShowing() &&
      this.isMouseDown &&
      this.isHovered &&
      !this.getIsFlagged() &&
      !this.getIsQuestioned()
    );
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

  public getIsQuestioned(): boolean {
    return this.square.isQuestioned;
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

.square.pressed,
.square.showing {
  border: 2px solid #808080;
}

.square-icon {
  height: 50%;
  width: auto;
}
</style>
