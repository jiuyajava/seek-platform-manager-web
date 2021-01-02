<template>
  <div class="container">
    <quill-editor
      ref="josEditor"
      v-model="content"
      class="qediter"
      :style="`height: ${height}px`"
      :options="editorOption"
      @change="onEditorChange"
    />
    <!-- 隐藏upload 上传图片 -->
    <el-upload
      ref="uploadImg"
      class="upload-img"
      :before-upload="picBeforeupload"
      :on-error="picError"
      accept="image/png, image/jpeg, image/jpg, image/gif"
      :show-file-list="false"
      :action="action"
      :on-success="picUploadSuccess"
    >
      <slot name="trigger">
        <div id="editorUploadImage" />
      </slot>
    </el-upload>
  </div>
</template>

<script>
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'
import { quillEditor } from 'vue-quill-editor'
import Quill from 'quill/dist/quill.js'
export default {
  name: 'Jos-Editor',
  components: {
    quillEditor
  },
  props: {
    value: {
      type: String,
      default: ''
    },
    option: {
      type: Object,
      default: () => ({})
    },
    height: {
      type: Number,
      default: 500
    }
  },
  data () {
    return {
      action: this.$http.adornUrl(`/sys/oss/upload?token=${this.$cookie.get('token')}`),
      content: '',
      editorOption: {},
      addImgRange: null
    }
  },
  watch: {
    value: {
      handler (newValue, preValue) {
        if (newValue !== preValue && newValue !== this.content) {
          this.content = newValue
        }
      },
      immediate: true
    }
  },
  created () {
    Object.assign(this.editorOption, this.option)
  },
  mounted () {
    this.init()
  },
  methods: {
    init () {
      // 重写图片添加图片
      const imgHandler = state => {
        if (state) {
          document.getElementById('editorUploadImage').click()
        }
      }
      this.$refs.josEditor.quill.getModule('toolbar').addHandler('image', imgHandler)
    },
    onEditorChange () {
      this.$emit('input', this.content)
    },
    // 图片大小检查
    picBeforeupload (file) {
      const isLt4M = file.size / 1024 / 1024 < 4
      if (!isLt4M) {
        this.$message.error('上传图片大小不能超过 4MB!')
      }
      return isLt4M
    },
    // 上传图片
    picUploadSuccess (res, file, fileList) {
      this.addImg(res.url)
    },
    // 上传图片失败
    picError () {
      this.$message({
        message: '图片添加失败，请重试',
        type: 'error'
      })
    },
    // 添加图片
    addImg (imgUrl) {
      this.addImgRange = this.$refs.josEditor.quill.getSelection() // 检索用户的选择范围, 如果编辑没有焦点，可能会返回一个null
      this.$refs.josEditor.quill.insertEmbed(
        this.addImgRange != null ? this.addImgRange.index : 0,
        'image',
        imgUrl,
        Quill.sources.USER
      )
    }
  }
}
</script>

<style lang="scss" scoped>
  .container{
    .qediter{
      margin-bottom: 80px;
      width: 92%;
    }
    .upload-img{
      display: none;
    }
  }
</style>