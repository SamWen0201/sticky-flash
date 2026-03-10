<script>
import WhiteBoard from "./components/WhiteBoard.vue";

export default {
  components: {
    WhiteBoard,
  },
  data() {
    return {};
  },
  methods: {},
};
</script>

<template>
  <main>
    <h1>Sticky Flash</h1>
    <WhiteBoard></WhiteBoard>
  </main>
</template>

<style lang="scss">
*,
*::after,
*::before {
  margin: 0;
  padding: 0;
  box-sizing: inherit;
}

html {
  font-family: "Courier New", Courier, monospace;
  // 1rem = 16px
}

body {
  box-sizing: border-box;
}
</style>
