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
      dragOffset: null, // 滑鼠點擊位置和便條紙位置的差值，保持這個差值去移動便條紙
      selectedNote: null, // 儲存 note.id 代表目前被拖曳的便條紙
    };
  },
  methods: {
    // add a Note on the whitebaord by the form
    addNote() {
      // easy check: contentEntering should have value

      const newNote = {
        content: "寫些什麼吧？",
        status: "alive",
        position: { x: 150, y: 150 },
      };

      // make post request
      supabase
        .post("/notes", newNote, {
          headers: { Prefer: "return=representation" },
        })
        .then((res) => {
          this.notes.push(res.data[0]);
          // clear the content
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
    deleteNote(deleteNote) {
      supabase
        .delete(`/notes?id=eq.${deleteNote.id}`)
        .then((res) => {
          console.log(res);
          this.notes = this.notes.filter((note) => note.id !== deleteNote.id);
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
    <div class="white-board__notes-number u-margin-top-md">
      You Got <span>{{ notes.length }}</span> Notes
    </div>
    <div class="form-addNote">
      <form v-on:submit.prevent="addNote">
        <!-- <label for="content">Note content (max 20 words): </label> -->

        <!-- <input id="content" maxlength="20" v-model="contentEntering" /> -->
        <button type="submit" class="addNoteTrigger">Add a note</button>
      </form>
    </div>

    <!-- Note component -->
    <Note
      v-for="note in notes"
      :key="note.id"
      :note="note"
      :class="{
        movingNote: note === selectedNote,
      }"
      @animationend="deleteNote(note)"
      @mousedown="setDragOffset(note, $event)"
      @trigger-delete-note="deletingAnimationOccur"
      @note-edited="updateNote"
    ></Note>
  </div>
</template>

<style scoped lang="scss">
@use "../assets/main.scss";

.white-board {
  position: relative;
  height: 90vh;

  &__notes-number {
    font-size: 1.2rem;
    font-weight: 300;
    color: #eee;
    letter-spacing: 0.1rem;

    span {
      font-weight: 500;
      font-size: 1.2rem;
      color: #fff;
    }
  }
}
.movingNote {
  transform: scale(1.3);
}
.form-addNote {
  position: absolute;
  bottom: 10%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.addNoteTrigger {
  background-color: burlywood;
  color: #ffffff;
  font-weight: 600;
  font-size: 1.1rem;
  border: none;
  border-radius: 2px;
  display: inline-block;
  padding: 1rem;
  box-shadow: 0 0.8rem 2.5rem rgba(0, 0, 0, 0.1);
  cursor: pointer;

  transition: all 0.3s;

  &:hover {
    box-shadow: 0 0.8rem 3rem rgba(0, 0, 0, 0.2);
    transform: translateY(-0.2rem);
  }

  &:active {
    transform: translateY(0.2rem);
    box-shadow: 0 0.8rem 2.5rem rgba(0, 0, 0, 0.1);
  }
}
</style>
