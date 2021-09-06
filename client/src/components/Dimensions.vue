<template>
  <div class="dimensions-container">
    <form @submit="onSubmit">
      <input
        ref="rows"
        :value="rows"
        type="text"
        @change="updateRows($event.target.value)"
      />
      <input
        ref="columns"
        :value="columns"
        @change="updateColumns($event.target.value)"
        type="text"
      />
      <button type="submit">Generate Board</button>
    </form>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";

interface NewDimensionsFunc {
  (): void;
}

interface RowChangeFunc {
  (rows: string): void;
}

interface ColumnChangeFunc {
  (columns: string): void;
}

interface NewBoardFunc {
  (): void;
}

@Component({
  props: [
    "generateNewBoard",
    "newDimensions",
    "rows",
    "columns",
    "columnChange",
    "rowChange",
    "updateRows",
    "updateColumns",
  ],
  components: {},
})
export default class Dimensions extends Vue {
  @Prop() public newDimensions!: NewDimensionsFunc;
  @Prop() public generateNewBoard!: NewBoardFunc;
  @Prop() public rows!: number;
  @Prop() public columns!: number;
  @Prop() public rowChange!: RowChangeFunc;
  @Prop() public columnChange!: ColumnChangeFunc;
  @Prop() public updateRows!: RowChangeFunc;
  @Prop() public updateColumns!: ColumnChangeFunc;

  constructor() {
    super();
  }

  onSubmit(e: InputEvent): void {
    e.preventDefault();
    this.newDimensions();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.dimensions-container {
  height: 50px;
  width: 100%;
  background: blue;
}
</style>
