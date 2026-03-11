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
  <main class="container">
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
  font-family:
    "Helvetica", "Arial", "LiHei Pro", "黑體-繁", "微軟正黑體", sans-serif;
  // 1rem = 16px
}

body {
  box-sizing: border-box;
  background-color: #cd995f;
  color: #eee;
  padding: 2rem;
}
.container {
}
</style>
