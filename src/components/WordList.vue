<template>
  <Word
      v-for="word in words"
      :key="word.name"
      :word="word"
      @toggleDialog="$emit('toggleDialog', word)"
      @deleteWord="$emit('deleteWord', word)" />
</template>

<script>
import Word from "./Word";

export default {
  name: "WordList",
  emits: [
    "toggleDialog",
    "deleteWord"
  ],
  components: {
    Word
  },
  props: {
    words: Array
  }
}
</script>

<style scoped>

</style>