<template>
  <NavBar
      @toggleDialog="showDialog"
      @export="exportWords"
      @import="importWords"
      @exportImage="exportImage"
      @exportImageNoAnswer="exportImageNoAnswer"
      @exportExam="exportExamButtonHandler"/>
  <div style="margin: 20px"></div>
  <div id="wordList">
    <WordList
        :words="entries"
        :showEditBtn="showEditBtn"
        :show-answer="showAnswer"
        @toggleDialog="showDialog"
        @deleteWord="deleteWord"/>
  </div>
  <el-dialog title="是否下载带答案的试题？" v-model="isAnsExamDialogVisible">
    <el-button @click="isAnsExamDialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="isAnsExamDialogVisible = false; exportExam(true, 'exam-ans.png')">确 定</el-button>
  </el-dialog>
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
              <el-option label="无" value=""></el-option>
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
import html2canvas from "html2canvas"

export default {
  name: 'App',
  components: {
    NavBar,
    WordList
  },
  methods: {
    exportExamButtonHandler() {
      this.messedWords = []
      let words = [...this.entries]
      words.sort(() => {
        return .5 - Math.random()
      })
      for (let word of words) {
        this.messedWords.push({
          type: word.type,
          name: word.name,
          definition: word.definitions[Math.floor((Math.random() * word.definitions.length))].definition
        })
      }
      this.exportExam(false, 'exam.png', words)
      this.isAnsExamDialogVisible = true
    },
    exportExam(hasAns, filename) {
      let canvas = document.createElement('canvas')
      let ctx = canvas.getContext('2d')
      canvas.width = 1240;
      canvas.height = 1754;
      ctx.fillStyle = "#FFFFFF";
      ctx.fillRect(0, 0, canvas.width, canvas.height);
      let rectH = 40;
      let rectW = 310;
      ctx.lineWidth = .5;
      ctx.fillStyle = "#000000";
      ctx.font = 'normal 16px PingFang SC'
      // 绘制表格
      // 第一步： 绘制横线
      for (let i = 0; i < canvas.width; i++) {
        ctx.moveTo(rectW * i, 0);
        //如果不设置moveTo，当前画笔没有位置
        ctx.lineTo(rectW * i, canvas.height);
      }
      // 第二步：绘制竖线：如果绘制的格子的宽高相等，可以将for循环放到一个里面；
      for (let i = 0; i < canvas.height; i++) {
        ctx.moveTo(0, rectH * i);
        ctx.lineTo(canvas.width, rectH * i);
      }
      ctx.stroke();

      let wordIndex = 0
      let left = true
      let words = this.messedWords
      for (let i = 0; i < canvas.height && wordIndex < words.length; wordIndex++) {
        let word = words[wordIndex]
        let definition = word.definition
        definition = word.type === 'phrase' ? '[词组] ' + definition : definition
        ctx.fillText(definition, (left ? 0 : 2 * rectW) + 10, rectH * i + 30);
        if (hasAns) {
          ctx.fillText(word.name, (left ? rectW : 3 * rectW) + 10, rectH * i + 30);
        }
        left = !left
        if (left) i++
      }

      let image = canvas.toDataURL('image/png')
      download(image, filename, 'image/png')
    },
    exportImage() {
      this.showEditBtn = false
      setTimeout(() => {
        html2canvas(document.getElementById('wordList'), {
          height: 1754,
          width: 1240
        }).then((canvas) => {
          let image = canvas.toDataURL('image/png')
          download(image, 'words.png', 'image/png')
          this.showEditBtn = true
        })
      }, 10)
    },
    exportImageNoAnswer() {
      this.showAnswer = false
      this.showEditBtn = false
      setTimeout(() => {
        html2canvas(document.getElementById('wordList'), {
          height: 1754,
          width: 1240
        }).then((canvas) => {
          let image = canvas.toDataURL('image/png')
          download(image, 'words-no-ans.png', 'image/png')
          this.showAnswer = true
          this.showEditBtn = true
        })
      }, 10)
    },
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
      showEditBtn: true,
      showAnswer: true,
      isEditFormVisible: false,
      isAnsExamDialogVisible: false,
      messedWords: [],
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
        },
        {
          type: "phrase",
          name: "flash by/through",
          definitions: [
            {
              form: "",
              definition: "飞驰"
            }
          ]
        },
        {
          type: "phrase",
          name: "in a flash",
          definitions: [
            {
              form: "",
              definition: "转瞬间，刹那间"
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
