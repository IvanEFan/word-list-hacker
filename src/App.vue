<template>
  <NavBar
      @toggleDialog="showDialog"
      @export="exportWords"
      @import="importWords"/>
  <div style="margin: 20px"></div>
  <WordList
      :words="entries"
      @toggleDialog="showDialog"
      @deleteWord="deleteWord"/>
  <el-dialog title="编辑" v-model="isEditFormVisible">
    <el-form :model="form" label-position="right" label-width="100px">
      <el-form-item label="类型">
        <el-select v-model="form.type" placeholder="请选择类型">
          <el-option label="单词" value="word"></el-option>
          <el-option label="词组" value="phrase"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="单词">
        <el-input v-model="form.name" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item
          v-for="(definition, index) in form.definitions"
          :label="'解释 ' + (index + 1)"
          :key="index"
      >
        <el-row :gutter="20">
          <el-col :span="8">
            <el-select v-model="definition.form">
              <el-option label="adj." value="adj."></el-option>
              <el-option label="n." value="n."></el-option>
              <el-option label="v." value="v."></el-option>
              <el-option label="adv." value="adv."></el-option>
              <el-option label="prep." value="prep."></el-option>
            </el-select>
          </el-col>
          <el-col :span="8">
            <el-input v-model="definition.definition" autocomplete="off"></el-input>
          </el-col>
          <el-col :span="8">
            <el-button @click="removeDefinition(definition)">删除</el-button>
          </el-col>
        </el-row>
      </el-form-item>
      <el-form-item>
        <el-row :gutter="20">
          <el-col :span="5">
            <el-button @click="addDefinition">添加解释</el-button>
          </el-col>
          <el-col :span="5">
            <el-button @click="isEditFormVisible = false">完成</el-button>
          </el-col>
        </el-row>
      </el-form-item>
    </el-form>
  </el-dialog>
</template>

<script>
import NavBar from "./components/NavBar";
import WordList from "./components/WordList";
import download from "downloadjs"

export default {
  name: 'App',
  components: {
    NavBar,
    WordList
  },
  methods: {
    exportWords() {
      download(this.getJson, 'words.json', 'text/plain')
    },
    importWords(content) {
      try {
        this.entries = JSON.parse(content)
        this.$notify.success({
          title: "导入成功",
          message: "已导入 " + this.entries.length + " 项内容"
        })
      } catch (SyntaxError) {
        this.$notify.error({
          title: "导入失败",
          message: "JSON 格式错误"
        })
      }
    },
    deleteWord(word) {
      let index = this.entries.indexOf(word);
      if (index !== -1) {
        this.entries.splice(index, 1)
      }
    },
    showDialog(word) {
      this.isEditFormVisible = true
      if (word) {
        this.form = word
      } else {
        this.form = {
          type: "word",
          name: "",
          definitions: []
        }
        this.entries.push(this.form)
      }
    },
    addDefinition() {
      this.form.definitions.push({
        form: "n.",
        definition: ""
      })
    },
    removeDefinition(item) {
      let index = this.form.definitions.indexOf(item);
      if (index !== -1) {
        this.form.definitions.splice(index, 1)
      }
    }
  },
  computed: {
    getJson: function () {
      return JSON.stringify(this.entries, null, 4)
    }
  },
  data: function () {
    return {
      isEditFormVisible: false,
      editMode: false,
      form: {
        type: "word",
        name: "",
        definitions: []
      },
      entries: [
        {
          type: "word",
          name: "aggressive",
          definitions: [
            {
              form: "adj.",
              definition: "好斗的"
            }
          ]
        },
        {
          type: "word",
          name: "comprehensive",
          definitions: [
            {
              form: "adj.",
              definition: "全面的，综合的"
            }
          ]
        }
      ]
    }
  }
}
</script>

<style scoped>
</style>
