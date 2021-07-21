<template>
  <el-divider></el-divider>
  <div
      v-for="word in words"
      :key="word.name">
    <Word
        :word="word"
        :definition-width="maxDefinitionWidth"
        :showEditBtn="showEditBtn"
        :show-answer="showAnswer"
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
        :show-answer="showAnswer"
        v-if="word.type === 'phrase'"
        @toggleDialog="$emit('toggleDialog', word)"
        @deleteWord="$emit('deleteWord', word)"/>
  </div>
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
    showEditBtn: Boolean,
    showAnswer: Boolean
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
.el-divider--horizontal {
  margin: 8px 0;
  background: 0 0;
  border-top: 1px dashed #e8eaec;
}
</style>