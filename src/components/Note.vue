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
    // emit trigger-delete-note event to whiteboard which make deleting animation happen
    triggerDeleteNote() {
      this.$emit("trigger-delete-note", this.note.id);
    },

    // enter into editing mode, and make editing input focus
    async editNote() {
      // isEditing must be true when editNote
      this.isEditing = true;
      // catch the input ref value
      await nextTick();
      // after dom re-render (state has changed)

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
    :class="{ deleting: note.status === 'deleting' }"
  >
    <div
      class="note__main"
      :class="{ deleting__main: note.status === 'deleting' }"
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
          maxlength="15"
        />
      </form>

      <!--p element display original note.content  -->
      <p class="note__content" v-else @click="editNote" ref="noteContentRef">
        {{ note.content }}
      </p>
    </div>

    <button
      class="note__delete-btn note__left-corner"
      :class="{ 'deleting__left-corner': note.status === 'deleting' }"
      type="button"
      @click="triggerDeleteNote"
    >
      &nbsp;
    </button>
    <!-- <span v-if="note.status === 'deleting'">deleting...</span> -->
  </div>
</template>
<style scoped lang="scss">
/* DELETING ANIMATION */
@keyframes deleting {
  0% {
    box-shadow: none;
    opacity: 1;
  }
  100% {
    box-shadow: none;
    opacity: 0;
  }
}
@keyframes deleting-main {
  0% {
    transform: rotateX(0) rotateY(0) rotateZ(0);
  }
  100% {
    transform: rotateX(40deg) rotateY(20deg) rotateZ(20deg);
  }
}
@keyframes deleting-left-corner {
  0% {
    transform: rotateX(0) rotateY(0) rotateZ(0);
  }
  100% {
    transform: rotateX(-40deg) rotateY(20deg) rotateZ(-20deg);
  }
}
@keyframes deleting-cut-line {
  0% {
    opacity: 1;
    width: 0;
  }
  100% {
    opacity: 0;
    width: 140%;
  }
}

.deleting {
  pointer-events: none;
  & {
    animation: deleting 1.2s;
    animation-fill-mode: forwards;
  }
  &__main {
    animation: deleting-main 1.2s;
    animation-fill-mode: forwards;
  }
  &__left-corner {
    animation: deleting-left-corner 1.2s;
    animation-fill-mode: forwards;
  }
}
// 1rem = 16px

.note {
  width: 11rem;
  height: 11rem;
  transition:
    opacity 0.4s,
    transform 0.3s;
  position: absolute;
  filter: drop-shadow(1rem 2rem 3rem rgba(0, 0, 0, 0.15));

  &:hover {
    cursor: grab;
  }
  &:active {
    cursor: grabbing;
  }

  &__main {
    padding: 1rem;
    clip-path: polygon(30% 0, 100% 0, 100% 100%, 0 100%, 0 30%);

    background-color: rgb(215, 215, 70);

    height: 100%;
    display: grid;
    align-items: center;
    align-content: stretch;
    justify-content: stretch;
  }

  // conteint-editing is input element
  &__content-editing,
  &__content {
    color: #4b4b4b;

    font-weight: 400;
    font-size: 1.2rem;
    padding: 1rem;
    transition: all 0.3s;
    text-align: center;
  }
  &__content-editing {
    width: 100%;
    border: none;
    outline: none;
  }
  &__content {
    display: block;
    width: 100%;
  }

  &__content {
    &:hover {
      background-color: rgb(208, 187, 27);
      cursor: pointer;
    }
  }

  &__delete-btn {
    position: absolute;
    top: 0;
    left: 0;
    border: none;
    width: 30%;
    height: 30%;
    padding: 0.5rem;
    background-color: rgb(180, 180, 50);
    color: #4b4b4b;
    cursor: pointer;
    clip-path: polygon(0 0, 100% 0, 0 100%);
    // opacity: 0;
  }

  // Implementing
}
</style>
