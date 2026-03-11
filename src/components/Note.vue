<script>
import { nextTick } from "vue";
export default {
  props: ["note"],
  data() {
    return {
      isEditing: false,
      editingContent: this.note.content,
    };
  },
  methods: {
    // emit delete-note event to WhiteBoard
    deleteNote() {
      this.$emit("delete-note", this.note.id);
    },

    // enter into editing mode, and make editing input focus
    async editNote() {
      // isEditing must be true when editNote
      this.isEditing = true;
      // catch the input ref value
      await nextTick();
      // after dom re-render (state has changed)
      console.log(this.$refs.inputRef);
      this.$refs.inputRef.focus();
    },
    // emit note-edited event and its args editingContent
    submitEditing() {
      if (this.isEditing === false) return;
      // if editedContent is empty
      if (this.editingContent === "") {
        this.editingContent = this.note.content;
        this.isEditing = false;
        return;
      }
      console.log("submitEditing is triggered!");
      this.$emit("note-edited", this.note, this.editingContent);
      this.isEditing = false;
    },
  },
};
</script>
<template>
  <div
    class="note"
    :style="{ left: note.position.x + 'px', top: note.position.y + 'px' }"
  >
    <!-- editing form input display editingContent-->
    <form
      class="note__editing-form"
      v-if="isEditing"
      @submit.prevent="submitEditing"
    >
      <input
        name="editedContent"
        class="note__content-editing"
        v-model="editingContent"
        ref="inputRef"
        @blur="submitEditing"
        maxlength="10"
      />
    </form>

    <!--p element display original note.content  -->
    <p class="note__content" v-else @click="editNote" ref="noteContentRef">
      {{ note.content }}
    </p>

    <button class="note__delete-btn" type="button" @click="deleteNote">
      cut
    </button>
    <!-- <span v-if="note.status === 'deleting'">deleting...</span> -->
  </div>
</template>
<style scoped lang="scss">
// 1rem = 16px

.note {
  background-color: rgba(224, 224, 28);
  color: black;
  font-weight: 300;
  font-style: italic;
  padding: 2rem;
  width: 10rem;
  height: 10rem;
  box-shadow: 0 2rem 3rem rgba(0, 0, 0, 0.4);
  transition:
    opacity 0.4s,
    transform 0.3s;
  position: absolute;
  display: grid;
  align-items: center;
  justify-content: center;

  &__content-editing {
    width: 100%;
    border: none;
    outline: none;
    padding: 0.2rem;
  }
  &__content {
    &:hover {
      background-color: rgb(206, 182, 24);
      cursor: pointer;
    }
  }

  &__delete-btn {
    position: absolute;
    top: 0.1rem;
    left: 0.1rem;
    border: none;
    padding: 0.5rem;
    border-radius: 50%;
    background-color: #fff;
    color: #4b4b4b;
    cursor: pointer;
  }
}
</style>
