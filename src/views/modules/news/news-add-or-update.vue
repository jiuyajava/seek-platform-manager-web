<template>
  <el-dialog
    :title="!dataForm.packageId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" label-width="80px">
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="类型" prop="type">
          <el-radio-group v-model="dataForm.type">
            <el-radio :label="1">通知</el-radio>
            <el-radio :label="2">新闻</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="标题" prop="title">
          <el-input v-model="dataForm.title" placeholder="标题" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
    </el-row>

    <el-form-item v-if="dataForm.type == 1" label="通知内容" prop="introduces">
      <el-input type="textarea" v-model="dataForm.content" placeholder="通知内容"   style="width: 92%"></el-input>
    </el-form-item>
    <el-form-item v-if="dataForm.type == 2" label="新闻内容" prop="content">
      <jos-editor ref="editor" 
        v-model="dataForm.content"
        @onClick="previewContent"
        style="width: 92%;"/>
    </el-form-item>

    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="排序" prop="orderNum">
          <el-input v-model="dataForm.orderNum" placeholder="排序" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
 
    </el-row>
 
    <template v-if="dataForm.id">
      <el-row type="flex">
        <el-col :span="11">
          <el-form-item label="创建时间" prop="createTime">
            <el-date-picker v-model="dataForm.createTime" type="datetime" placeholder="创建时间" style="width: 80%;" readonly></el-date-picker>
          </el-form-item>
        </el-col>
        
      </el-row>
    </template>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<style>
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
  .tox-tinymce-aux {
    z-index: 3000 !important;
  }
</style>

<script>
  import JosEditor from '@/components/tinymce-editor'
  export default {
    components: {
      JosEditor
    },
    data () {
      return {
        url: '',
        fileList: [],
        visible: false,
        dataForm: {
          id: 0,
          type: 1,
          title: '',
          content: '',
          orderNum: '',
          createTime: ''
        },
        dataRule: {
          title: [
            { required: true, message: '标题不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      previewContent (e, editor) {
        console.log(this.dataForm.details)
      },
      init (id) {
        debugger
        this.fileList = []
        this.dataForm.thumbnail = null
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/manager/newsHelp/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.id = data.newsHelp.id
                this.dataForm.type = data.newsHelp.type
                this.dataForm.title = data.newsHelp.title
                this.dataForm.content = data.newsHelp.content
                this.dataForm.orderNum = data.newsHelp.orderNum
                this.dataForm.createTime = data.newsHelp.createTime
              } else {
                this.fileList = []
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/manager/newsHelp/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'type': this.dataForm.type,
                'title': this.dataForm.title,
                'content': this.dataForm.content,
                'orderNum': this.dataForm.orderNum,
                'createTime': this.dataForm.createTime
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      },
      beforeUpload (file) {
        const isLt2M = file.size / 1024 / 1024 < 2
        if (file.type !== 'image/jpg' && file.type !== 'image/jpeg' && file.type !== 'image/png' && file.type !== 'image/gif') {
          this.$message.error('只支持jpg、png、gif格式的图片！')
          return false
        }
        if (!isLt2M) {
          this.$message.error('上传单张图片大小不能超过 2MB!')
          return false
        }
        return true
      },
      handleThumbnailSuccess (res, file, fileList) {
        if (res.code === 0) {
          this.dataForm.thumbnail = res.url
        } else {
          this.$message.error('网络错误，上传失败，请重试！')
        }
      },
      handleThumbnailRemove () {
        this.dataForm.thumbnail = null
      },
      handleRemove (file, fileList) {
        if (fileList.length > 0) {
          this.dataForm.images = fileList.map(item => {
            return item.url
          }).join(',')
        } else {
          this.dataForm.images = null
        }
        this.fileList = fileList
      },
      handleSuccess (res, file, fileList) {
        this.fileList.push({
          name: file.name,
          url: res.url
        })
        if (fileList.length > 0) {
          this.dataForm.images = this.fileList.map(item => {
            return item.url
          }).join(',')
        } else {
          this.dataForm.images = null
          this.fileList = null
        }
      },
      handleExceed (files, fileList) {
        alert('文件超出个数限制!')
      }
    }
  }
</script>
