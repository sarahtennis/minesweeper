<template>
  <div class="row">
    <Square
      v-for="square in row"
      :key="square.key"
      :square="square"
      :isMouseDown="isMouseDown"
    />
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import Square from "./Square.vue";

@Component({
  components: {
    Square,
  },
})
export default class Board extends Vue {
  @Prop() public row!: string[];
  @Prop() public isMouseDown!: string[];
  constructor() {
    super();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.row {
  display: flex;
}
</style>
