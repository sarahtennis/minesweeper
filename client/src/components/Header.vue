<template>
  <div id="header">
    <button @click="$emit('newBoard')">
      <img src="../assets/Smiley2.svg" />
    </button>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

@Component({
  components: {},
})
export default class Header extends Vue {
  constructor() {
    super();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
#header {
  display: flex;
  justify-content: center;
  width: 100%;
}
</style>
