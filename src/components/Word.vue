<template>
  <el-row>
    <div :style="'width:' + (definitionWidth + 20) + 'px'">{{ formattedDef }}</div>
    <el-col :span="10">
      {{ word.name }}
    </el-col>
    <el-col :span="2" v-if="showEditBtn">
      <el-button type="text" size="mini" @click="$emit('toggleDialog')">编辑</el-button>
      <el-button type="text" size="mini" @click="$emit('deleteWord')">删除</el-button>
    </el-col>
  </el-row>
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
    }
  },
  props: {
    word: Object,
    definitionWidth: Number,
    showEditBtn: Boolean
  }
}
</script>

<style scoped>

</style>