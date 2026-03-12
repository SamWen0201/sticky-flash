<script>
import WhiteBoard from "./components/WhiteBoard.vue";

export default {
  components: {
    WhiteBoard,
  },
  data() {
    return {
      loading: true,
    };
  },
  methods: {
    loadingFinished() {
      this.loading = false;
    },
  },
};
</script>

<template>
  <main class="container">
    <h1>Sticky Flash</h1>
    <div v-if="loading" class="loading">
      <span class="loading__text">Loading...</span>
    </div>
    <WhiteBoard
      :loading="loading"
      @loading-finished="loadingFinished"
    ></WhiteBoard>
  </main>
</template>

<style lang="scss">
@use "./assets/scss/variables.scss" as *;

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
// LOADING STYLE
.loading {
  background-color: rgba($loading-grey-color, 0.3);
  height: 100%;
  width: 100%;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1000;
  transition: all 0.2s;

  &__text {
    position: fixed;

    color: #4b4b4b;
    font-size: 1.5rem;
    font-weight: 600;
    z-index: 2000;
    display: inline-block;
    padding: 2rem;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    transition: all 0.2s;
  }
}
</style>
