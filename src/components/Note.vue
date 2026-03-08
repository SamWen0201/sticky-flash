<script>
export default {
  props: ["note"],
  data() {
    return {};
  },
  methods: {
    deleteNote() {
      console.log("call deleteNote in Note");
      this.$emit("delete-note", this.note.id);
    },
  },
};
</script>
<template>
  <div class="note">
    <div>{{ note.content }}</div>
    <button type="button" @click="deleteNote">delete</button>
  </div>
</template>
<style scoped>
.note {
  background-color: rgb(224, 224, 28);
  padding: 2rem;
  display: inline-block;
  width: 6rem;
  height: 6rem;
  box-shadow: 0 1rem 3rem rgba(#000, 0.1);
  transition: all 0.4s;
}
</style>
