<template>
    <el-row>
      <el-col :span="2">
        <el-button @click="$emit('toggleDialog')">添加</el-button>
      </el-col>
      <el-col :span="2">
        <el-button @click="$emit('export')">导出</el-button>
      </el-col>
      <el-col :span="2">
        <el-upload
            action=""
            :on-change="handleUpload"
            :show-file-list="false"
            :auto-upload="false"
            accept=".json"
        >
          <el-button>导入</el-button>
        </el-upload>
      </el-col>
      <el-col :span="4">
        <el-button @click="$emit('exportImage')">导出图片</el-button>
      </el-col>
    </el-row>
</template>

<script>
export default {
  name: "NavBar",
  emits: [
    "toggleDialog",
    "export",
    "import",
    "exportImage"
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