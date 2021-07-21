<template>
  <el-row>
    <div :style="'width:' + (finalDefinitionWidth + 20) + 'px'">{{ formattedDef }}</div>
    <el-col :span="10">
      <div v-if="showAnswer">
        {{ word.name }}
        <el-tag v-if="word.type === 'phrase'" size="mini">词组</el-tag>
      </div>
    </el-col>
    <el-col :span="2" v-if="showEditBtn">
      <el-button type="text" size="mini" @click="$emit('toggleDialog')">编辑</el-button>
      <el-button type="text" size="mini" @click="$emit('deleteWord')">删除</el-button>
    </el-col>
  </el-row>
  <el-divider></el-divider>
</template>

<script>
export default {
  name: "Word",
  emits: [
    "toggleDialog",
    "deleteWord"
  ],
  computed: {
    formattedDef: function () {
      let ret = ""
      this.word.definitions.forEach((item) => {
        ret += `${item.form} ${item.definition}；`
      })
      return ret.substr(0, ret.length - 1)
    },
    finalDefinitionWidth: function () {
      return this.definitionWidth > this.maxDefinitionWidth ? this.maxDefinitionWidth : this.definitionWidth
    }
  },
  props: {
    word: Object,
    definitionWidth: Number,
    showEditBtn: Boolean,
    showAnswer: Boolean
  },
  data: function () {
    return {
      maxDefinitionWidth: 1000
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