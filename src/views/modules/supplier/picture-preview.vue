<template>
  <el-dialog
    title="营业执照预览"
    :center="true"
    :close-on-click-modal="true"
    :visible.sync="visible">
      <el-image 
        :src="url"
        fit="contain">
      </el-image>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        url: '',
        srcList: [],
        visible: false
      }
    },
    methods: {
      init (imgUrl) {
        this.url = imgUrl
        this.visible = true
      }
    }
  }
</script>