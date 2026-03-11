<script>
import Note from "./Note.vue";
import supabase from "@/api/supabase";

export default {
  mounted() {
    // axios get /notes
    const data = supabase.get("/notes");
    data
      .then((response) => {
        this.notes = response.data;
      })
      .catch((error) => console.log(error));
  },
  components: {
    Note,
  },

  data() {
    return {
      notes: [],
      contentEntering: "",
      dragOffset: null, // 滑鼠點擊位置和便條紙位置的差值，保持這個差值去移動便條紙
      selectedNote: null, // 儲存 note.id 代表目前被拖曳的便條紙
    };
  },
  methods: {
    // add a Note on the whitebaord by the form
    addNote() {
      // easy check: contentEntering should have value
      if (this.contentEntering === "" || this.contentEntering === null) {
        alert("Do let Note content empty");
        return;
      }

      const newNote = {
        content: this.contentEntering,
        status: "alive",
        position: { x: 0, y: 5 },
      };

      // make post request
      supabase
        .post("/notes", newNote, {
          headers: { Prefer: "return=representation" },
        })
        .then((res) => {
          this.notes.push(res.data[0]);
          // clear the content
          this.contentEntering = "";
        })
        .catch((err) => {
          console.log(err);
          // render Add note failed
        })
        .finally(() => console.log("finally block"));
    },

    updateNote(editedNote, editedContent) {
      if (editedNote.content === editedContent) return;
      supabase
        .patch(`/notes?id=eq.${editedNote.id}`, { content: editedContent })
        .then((res) => {
          console.log(res);
          this.notes = this.notes.map((note) => {
            if (note.id === editedNote.id) {
              return {
                id: note.id,
                content: editedContent,
                status: note.status,
                position: note.position,
              };
            }
            return note;
          });
        })
        .catch((err) => console.log(err))
        .finally(() => console.log("Finally block of updateNote"));
    },

    // delete a Note
    deleteNote(id) {
      supabase
        .delete(`/notes?id=eq.${id}`)
        .then((res) => {
          console.log(res);
          this.notes = this.notes.filter((note) => note.id !== id);
        })
        .catch((err) => console.log(err))
        .finally(() => console.log("Finally block of deleteNote"));
    },

    // deleting animation happens before the deleteNote
    deletingAnimationOccur(id) {
      this.notes = this.notes.map((note) => {
        if (note.id === id) {
          // change the status value of note object, the status changed will trigger the animation
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
        x: event.clientX - note.position.x,
        y: event.clientY - note.position.y,
      };
    },

    // drag note
    dragNote(event) {
      if (this.dragOffset === null || this.selectedNote === null) {
        return;
      }

      // remove the relation value of x and y
      const x = event.clientX - this.dragOffset.x;
      const y = event.clientY - this.dragOffset.y;

      // bind the selected Note position with the offset between Note and pointer
      this.selectedNote.position = {
        x,
        y,
      };
      // console.log("dragNote is triggered");
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
    ref="whiteBoardRef"
  >
    <form v-on:submit.prevent="addNote">
      <label for="content">Note content (max 20 words): </label>

      <input id="content" maxlength="20" v-model="contentEntering" />
      <button type="submit">submit</button>
    </form>

    <!-- Note component -->
    <Note
      v-for="note in notes"
      :key="note.id"
      :note="note"
      :class="{
        deletingAnimationOccur: note.status === 'deleting',
        movingNote: note === selectedNote,
      }"
      @animationend="deleteNote(note.id)"
      @mousedown="setDragOffset(note, $event)"
      @delete-note="deletingAnimationOccur"
      @note-edited="updateNote"
    ></Note>
  </div>
</template>

<style scoped>
/* DELETING ANIMATION */
@keyframes deleting {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}

.deletingAnimationOccur {
  animation: deleting 2s;
  animation-fill-mode: forwards;
}

.white-board {
  position: relative;

  /* background-color: #cd995f; */
  height: 20rem;
}
.movingNote {
  transform: scale(1.3);
}
</style>
