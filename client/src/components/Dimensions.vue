<template>
  <div class="dimensions-container">
    <form @submit="onSubmit">
      <input
        ref="rows"
        v-model="newRows"
        type="text"
        @keydown="onKeyDown($event)"
      />
      <input
        ref="columns"
        v-model="newColumns"
        @keydown="onKeyDown($event)"
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

  public newRows: string;
  public newColumns: string;

  constructor() {
    super();
    this.newRows = this.rows.toString();
    this.newColumns = this.columns.toString();
  }

  onKeyDown(event: KeyboardEvent): void {
    const newRegex = new RegExp("^[0-9]*$");
    const value = event.key;
    if (value !== "Backspace" && !value.match(newRegex)) {
      event.preventDefault();
    }
  }

  onSubmit(e: InputEvent) {
    e.preventDefault();
    this.$emit("updateDimensions", {
      rows: Number(this.newRows),
      columns: Number(this.newColumns),
    });
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
