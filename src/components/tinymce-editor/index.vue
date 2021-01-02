<template>
  <div class="tinymce-editor">
    <editor v-model="content"
      :init="init"
      :disabled="disabled"
      :style="`height: ${height}px`"
      @onClick="onClick">
    </editor>
  </div>
</template>

<script>
import tinymce from 'tinymce/tinymce'
import Editor from '@tinymce/tinymce-vue'
import 'tinymce/themes/silver'
// 编辑器更多插件参考：https://www.tiny.cloud/docs/plugins/
import 'tinymce/plugins/image'
import 'tinymce/plugins/media'
import 'tinymce/plugins/table'
import 'tinymce/plugins/lists'
import 'tinymce/plugins/wordcount'
import 'tinymce/plugins/code'
import 'tinymce/plugins/preview'
import 'tinymce/icons/default/icons'
import axios from 'axios'

export default {
  name: 'Jos-Editor',
  components: {
    Editor
  },
  props: {
    value: {
      type: String,
      default: ''
    },
    baseUrl: {
      type: String,
      default: process.env.NODE_ENV === 'production' ? window.SITE_CONFIG.cdnUrl : ''
    },
    disabled: {
      type: Boolean,
      default: false
    },
    height: {
      type: Number,
      default: 500
    },
    plugins: {
      type: [String, Array],
      default: 'lists image media table wordcount preview code'
    },
    toolbar: {
      type: [String, Array],
      default: 'undo redo |  formatselect | bold italic forecolor backcolor | alignleft aligncenter alignright alignjustify | lists image table preview code | media | bullist numlist outdent indent | removeformat '
    }
  },
  data () {
    return {
      init: {
        language: 'zh_CN',
        language_url: `${this.baseUrl}/static/plugins/tinymce/langs/zh_CN.js`,
        skin_url: `${this.baseUrl}/static/plugins/tinymce/skins/ui/oxide`,
        content_css: `${this.baseUrl}/static/plugins/tinymce/skins/content/default/content.css`,
        // skin_url: `${this.baseUrl}/tinymce/skins/ui/oxide-dark`, // 暗色系
        // content_css: `${this.baseUrl}/tinymce/skins/content/dark/content.css`, // 暗色系
        plugins: this.plugins,
        toolbar: this.toolbar,
        branding: false,
        menubar: false,
        // 此处为图片上传处理函数，这个直接用了base64的图片形式上传图片，
        // 如需ajax上传可参考https://www.tiny.cloud/docs/configure/file-image-upload/#images_upload_handler
        images_upload_handler: (blobInfo, success, failure) => {
          const formdata = new FormData()
          formdata.set('file', blobInfo.blob())
          axios.post(this.$http.adornUrl(`/sys/oss/upload?token=${this.$cookie.get('token')}`), formdata).then(res => {
            console.log(res.data.url)
            success(res.data.url)
          }).catch(res => {
            failure('error')
          })
        }
      },
      content: this.value
    }
  },
  mounted () {
    tinymce.init({
      language: 'zh_CN',
      language_url: `${this.baseUrl}/static/plugins/tinymce/langs/zh_CN.js`,
      skin_url: `${this.baseUrl}/static/plugins/tinymce/skins/ui/oxide`,
      content_css: `${this.baseUrl}/static/plugins/tinymce/skins/content/default/content.css`,
      // skin_url: `${this.baseUrl}/tinymce/skins/ui/oxide-dark`, // 暗色系
      // content_css: `${this.baseUrl}/tinymce/skins/content/dark/content.css`, // 暗色系
      plugins: this.plugins,
      toolbar: this.toolbar,
      branding: false,
      menubar: false,
      // 此处为图片上传处理函数，这个直接用了base64的图片形式上传图片，
      // 如需ajax上传可参考https://www.tiny.cloud/docs/configure/file-image-upload/#images_upload_handler
      images_upload_handler: (blobInfo, success, failure) => {
        const formdata = new FormData()
        formdata.set('file', blobInfo.blob())
        axios.post(this.$http.adornUrl(`/sys/oss/upload?token=${this.$cookie.get('token')}`), formdata).then(res => {
          console.log(res.data.url)
          success(res.data.url)
        }).catch(res => {
          failure('error')
        })
      },
      images_upload_credentials: true
    })
  },
  methods: {
    // 添加相关的事件，可用的事件参照文档=> https://github.com/tinymce/tinymce-vue => All available events
    onClick (e) {
      this.$emit('onClick', e, tinymce)
    },
    // 可以添加一些自己的自定义事件，如清空内容
    clear () {
      this.content = ''
    }
  },
  watch: {
    value (newValue) {
      this.content = newValue
    },
    content (newValue) {
      this.$emit('input', newValue)
    }
  }
}
</script>
