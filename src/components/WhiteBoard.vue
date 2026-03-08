<script>
import Note from "./Note.vue";
export default {
  components: {
    Note,
  },
  data() {
    return {
      notes: [
        { id: crypto.randomUUID(), content: "First Note!", status: "alive" },
      ],
      contentEntering: "",
    };
  },
  methods: {
    addNote() {
      if (this.contentEntering === "" || this.contentEntering === null) {
        alert("Do let Note content empty");
        return;
      }
      this.notes.push({
        id: crypto.randomUUID(),
        content: this.contentEntering,
        status: "alive",
      });
      this.contentEntering = "";
      this.formOpen = false;
    },
    deleteNote(id) {
      console.log("call deleteNote in WhiteBoard");
      this.notes = this.notes.filter((note) => note.id !== id);
    },
    deletingAnimation(id) {
      this.notes = this.notes.map((note) => {
        if (note.id === id) {
          console.log("return deleting note: ", note.id);
          return {
            id: note.id,
            content: note.content,
            status: "deleting",
          };
        }
        return note;
      });
    },
  },
};
</script>

<template>
  <h2>WhiteBoard</h2>

  <form>
    <label for="content">Note content (max 10 words): </label>
    <input id="content" v-model="contentEntering" maxlength="10" />
    <button type="button" @click="addNote">submit</button>
  </form>

  <Note
    v-for="(note, index) in notes"
    :key="index"
    :note="note"
    @delete-note="deletingAnimation"
    :class="{ deletingAnimation: note.status === 'deleting' }"
    @animationend="deleteNote(note.id)"
  ></Note>
</template>

<style scoped>
@keyframes deleting {
  0% {
    background-color: yellow;
  }
  100% {
    background-color: yellowgreen;
  }
}
.deletingAnimation {
  animation: deleting 4s;
  animation-fill-mode: forwards;
}
</style>
