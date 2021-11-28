<template>
  <svg
    xmlns="http://www.w3.org/2000/svg"
    width="52"
    height="92"
    viewBox="0 0 50 92"
  >
    <!-- top -->
    <path
      :class="{ highlight: getIsHighlight(0) }"
      d="M 10,8 L 14,4 L 42,4 L 46,8 L 42,12 L 14,12 L 10,8 z"
    />
    <!-- top right -->
    <path
      :class="{ highlight: getIsHighlight(1) }"
      fill="black"
      d="M 48,10 L 52,14 L 52,42 L 48,46 L 44,42 L 44,14 L 48,10 z"
    />
    <!-- bottom right -->
    <path
      :class="{ highlight: getIsHighlight(2) }"
      fill="black"
      d="M 48,50 L 52,54 L 52,82 L 48,86 L 44,82 L 44,54 L 48,50 z"
    />
    <!-- bottom -->
    <path
      :class="{ highlight: getIsHighlight(3) }"
      fill="black"
      d="M 10,88 L 14,84 L 42,84 L 46,88 L 42,92 L 14,92 L 10,88 z"
    />
    <!-- bottom left -->
    <path
      :class="{ highlight: getIsHighlight(4) }"
      fill="black"
      d="M 8,50 L 12,54 L 12,82 L 8,86 L 4,82 L 4,54 L 8,50 z"
    />
    <!-- top left -->
    <path
      :class="{ highlight: getIsHighlight(5) }"
      fill="black"
      d="M 8,10 L 12,14 L 12,42 L 8,46 L 4,42 L 4,14 L 8,10 z"
    />
    <!-- center -->
    <path
      :class="{ highlight: getIsHighlight(6) }"
      fill="black"
      d="M 10,48 L 14,44 L 42,44 L 46,48 L 42,52 L 14,52 L 10,48 z"
    />
  </svg>
</template>

<script>
export const Numbers = {
  EMPTY: [false, false, false, false, false, false, false],
  ZERO: [true, true, true, true, true, true, false],
  ONE: [false, true, true, false, false, false, false],
  TWO: [true, true, false, true, true, false, true],
  THREE: [true, true, true, true, false, false, true],
  FOUR: [false, true, true, false, false, true, true],
  FIVE: [true, false, true, true, false, true, true],
  SIX: [true, false, true, true, true, true, true],
  SEVEN: [true, true, true, false, false, false, false],
  EIGHT: [true, true, true, true, true, true, true],
  NINE: [true, true, true, false, false, true, true],
};

export default {
  props: ["number"],
  methods: {
    getIsHighlight(index) {
      return this.number[index];
    },
  },
};
</script>

<style scoped>
path {
  fill: rgba(128, 0, 0, 0.2);
}

path.highlight {
  fill: red;
}
</style>
