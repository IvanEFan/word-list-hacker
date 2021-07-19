<template>
  <div
      v-for="word in words"
      :key="word.name">
    <Word
        :word="word"
        :definition-width="maxDefinitionWidth"
        :showEditBtn="showEditBtn"
        v-if="word.type === 'word'"
        @toggleDialog="$emit('toggleDialog', word)"
        @deleteWord="$emit('deleteWord', word)"/>
  </div>
  <div
      v-for="word in words"
      :key="word.name">
    <Word
        :word="word"
        :definition-width="maxDefinitionWidth"
        :showEditBtn="showEditBtn"
        v-if="word.type === 'phrase'"
        @toggleDialog="$emit('toggleDialog', word)"
        @deleteWord="$emit('deleteWord', word)"/>
  </div>
  <!--  <Word-->
  <!--      v-for="word in words"-->
  <!--      :key="word.name"-->
  <!--      :word="word"-->
  <!--      :definition-width="maxDefinitionWidth"-->
  <!--      :showEditBtn="showEditBtn"-->
  <!--      @toggleDialog="$emit('toggleDialog', word)"-->
  <!--      @deleteWord="$emit('deleteWord', word)"/>-->
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
    words: Array,
    showEditBtn: Boolean
  },
  computed: {
    maxDefinitionWidth: function () {
      let getFormattedDef = function (word) {
        let ret = ""
        word.definitions.forEach((item) => {
          ret += `${item.form} ${item.definition}；`
        })
        return ret.substr(0, ret.length - 1)
      }
      let pxWidth = function (font, text) {
        let canvas = document.createElement("canvas")
        let context = canvas.getContext("2d");
        font && (context.font = font);
        let metrics = context.measureText(text);

        return metrics.width;
      }
      let max = 0
      for (let word of this.words) {
        let width = pxWidth('normal 16px PingFang SC', getFormattedDef(word))
        max = width > max ? width : max
      }
      return Math.ceil(max)
    }
  }
}
</script>

<style scoped>

</style>