<template>
  <div
    class="square"
    :class="{
      bomb: getIsBomb(),
      hovered: getIsHovered(),
      showing: getIsShowing(),
    }"
  >
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

  mounted(): void {
    this.$el.addEventListener("mousedown", (event: MouseEvent) => {
      this.isHovered = true;
      event.preventDefault();
    });

    this.$el.addEventListener("mouseup", (event: MouseEvent) => {
      this.isHovered = false;
    });

    this.$el.addEventListener("click", () => {
      this.square.isShowing = true;
    });

    this.$el.addEventListener("mouseenter", (event: MouseEvent) => {
      if (this.isMouseDown) {
        this.isHovered = true;
      } else {
        this.isHovered = false;
      }
    });

    this.$el.addEventListener("mouseleave", (event: MouseEvent) => {
      this.isHovered = false;
    });
  }

  public getIsHovered(): boolean {
    return this.isHovered;
  }

  public getIsBomb(): boolean {
    return this.square.isBomb;
  }

  public getIsShowing(): boolean {
    return this.square.isShowing;
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
}

.square.hovered,
.square.showing {
  border: 2px solid #666;
}

.bomb.showing {
  border: 1px solid red;
}
</style>
