<template>
    <el-row>
        <el-button @click="$emit('toggleDialog')">添加</el-button>
        <el-button @click="$emit('export')">导出</el-button>
        <el-upload
            action=""
            :on-change="handleUpload"
            :show-file-list="false"
            :auto-upload="false"
            accept=".json"
        >
          <el-button>导入</el-button>
        </el-upload>
        <el-button @click="$emit('exportImage')">导出图片</el-button>
        <el-button @click="$emit('exportImageNoAnswer')">导出无答案图片</el-button>
        <el-button @click="$emit('exportExam')">导出测试</el-button>
    </el-row>
</template>

<script>
export default {
  name: "NavBar",
  emits: [
    "toggleDialog",
    "export",
    "import",
    "exportImage",
    "exportImageNoAnswer",
    "exportExam"
  ],
  data: function () {
    return {
      showEditBtn: true
    }
  },
  methods: {
    handleUpload(file) {
      let reader = new FileReader()
      reader.readAsText(file.raw)
      reader.onload = (e) => {
        const content = e.target.result
        this.$emit('import', content)
      }
    }
  }
}
</script>

<style scoped>

</style>