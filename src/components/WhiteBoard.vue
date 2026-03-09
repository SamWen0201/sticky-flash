<script>
import Note from "./Note.vue";
export default {
  components: {
    Note,
  },

  data() {
    return {
      notes: [
        {
          id: crypto.randomUUID(),
          content: "First Note!",
          status: "alive",
          position: { x: 0, y: 5 },
        },
      ],
      contentEntering: "",
      dragOffset: null, // 滑鼠點擊位置和便條紙位置的差值，保持這個差值去移動便條紙
      selectedNote: null, // 儲存 note.id 代表目前被拖曳的便條紙
    };
  },
  methods: {
    addNote() {
      console.log("addNote is triggered!");

      if (this.contentEntering === "" || this.contentEntering === null) {
        alert("Do let Note content empty");
        return;
      }

      this.notes.push({
        id: crypto.randomUUID(),
        content: this.contentEntering,
        status: "alive",
        position: { x: 0, y: 5 },
      });

      this.contentEntering = "";
      this.formOpen = false;
    },
    deleteNote(id) {
      // console.log("call deleteNote in WhiteBoard");
      this.notes = this.notes.filter((note) => note.id !== id);
    },

    // delete animation happens before the deleteNote
    deletingAnimation(id) {
      this.notes = this.notes.map((note) => {
        if (note.id === id) {
          // console.log("return deleting note: ", note.id);
          return {
            id: note.id,
            content: note.content,
            status: "deleting",
            position: note.position,
          };
        }
        return note;
      });
    },

    // set selected note position, and restore the offset of pointer position and note position
    setDragOffset(note, event) {
      /**
       * 1. 記住是哪張便條紙被點到（selectedNote）
         2. 取得滑鼠目前的位置（event.clientX、event.clientY）
         3. 計算 offset：滑鼠位置 - note.position
      */
      this.selectedNote = note;
      this.dragOffset = {
        x: note.position.x - event.clientX,
        y: note.position.y - event.clientY,
      };
    },

    dragNote(event) {
      if (this.dragOffset === null || this.selectedNote === null) {
        return;
      }

      // bind the selected Note position with the offset between Note and pointer
      this.selectedNote.position = {
        x: event.clientX + this.dragOffset.x,
        y: event.clientY + this.dragOffset.y,
      };
    },

    // remove the drag note
    removeDrag() {
      this.selectedNote = null;
      this.dragOffset = null;
    },

    // Limit the note dragging action scope only in whiteboard
  },
};
</script>

<template>
  <div
    class="white-board"
    @mousemove="dragNote"
    @mouseup="removeDrag"
    ref="whiteboradRef"
  >
    <h2>WhiteBoard</h2>

    <form v-on:submit.prevent="addNote">
      <label for="content">Note content (max 20 words): </label>

      <input id="content" maxlength="20" v-model="contentEntering" />
      <button type="submit">submit</button>
      <span>input: {{ contentEntering }}</span>
    </form>

    <Note
      v-for="note in notes"
      :key="note.id"
      :note="note"
      @delete-note="deletingAnimation"
      :class="{
        deletingAnimation: note.status === 'deleting',
        movingNote: note === selectedNote,
      }"
      @animationend="deleteNote(note.id)"
      @mousedown="setDragOffset(note, $event)"
      ref="noteRef"
    ></Note>
  </div>
</template>

<style scoped>
/* DELETING ANIMATION */
@keyframes deleting {
  0% {
    background-color: yellow;
  }
  100% {
    background-color: rgb(160, 180, 120);
  }
}

.deletingAnimation {
  animation: deleting 2s;
  animation-fill-mode: forwards;
}

.white-board {
  position: relative;

  background-color: #cd995f;
  height: 20rem;
}
.movingNote {
  transform: scale(1.3);
}
</style>
