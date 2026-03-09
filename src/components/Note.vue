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
  <div
    class="note"
    :style="{ left: note.position.x + 'px', top: note.position.y + 'px' }"
  >
    <div>{{ note.content }}</div>
    <button type="button" @click="deleteNote">delete</button>
    <span v-if="note.status === 'deleting'">deleting...</span>
  </div>
</template>
<style scoped>
.note {
  background-color: rgba(224, 224, 28);
  padding: 2rem;
  display: block;
  width: 6rem;
  height: 6rem;
  box-shadow: 0 2rem 3rem rgba(0, 0, 0, 0.4);
  transition:
    background-color 0.4s,
    transform 0.3s;
  position: absolute;
}
</style>
