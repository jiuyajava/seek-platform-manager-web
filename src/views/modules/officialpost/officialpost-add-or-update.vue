<template>
  <el-dialog
    :title="'新增'"
    :close-on-click-modal="false"
    :visible.sync="visible"
  >
    <el-form :model="dataForm" ref="dataForm"  :rules="dataRule"  label-width="80px">
      <el-row>
        <el-col :span="8"
          ><div class="grid-content bg-purple">
            <el-form-item label="ID" prop="id">
              <el-input :disabled="true"
                v-model="dataForm.id"
              ></el-input>
            </el-form-item></div
        ></el-col>
        <el-col :span="8"
          ><div class="grid-content bg-purple">
            <el-form-item label="标题">
              <el-input v-model="dataForm.title" ></el-input>
            </el-form-item></div
        ></el-col>
        </el-row>
        <el-row>
        <el-col
          ><div class="grid-content bg-purple-light">
           <el-form-item
              label="缩率图"
              v-model="dataForm.thumbnail">
            <el-upload
              class="avatar-uploader"
              :action="url"
              :show-file-list="false"
              :on-success="handleThumbnailSuccess"
              :on-remove="handleThumbnailRemove"
              :before-upload="beforeUpload">
              <img v-if="dataForm.thumbnail" :src="dataForm.thumbnail" class="avatar">
              <i v-else class="el-icon-plus avatar-uploader-icon"></i>
            </el-upload>
          </el-form-item>
            </div
        ></el-col>
      </el-row>
        <el-row>
        <el-col
          ><div class="grid-content bg-purple-light">
            <el-form-item label="内容" prop="code">
               <jos-editor v-model="dataForm.content" />
            </el-form-item></div
        ></el-col>
      </el-row>
      <el-row>
          <el-col :span="8"
          ><div class="grid-content bg-purple-light">
            <el-form-item label="标签">
              <el-input v-model="dataForm.postType" ></el-input>
            </el-form-item></div
        ></el-col>
        <el-col :span="8"
          ><div class="grid-content bg-purple-light">
            <el-form-item label="状态">
              <el-select v-model="dataForm.isDelete" >
                <el-option
                  v-for="item in isDeleteOptions"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                >
                </el-option>
              </el-select>
            </el-form-item></div
        ></el-col>
      </el-row>
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
      isDeleteOptions: [
        {
          value: 0,
          label: '启用'
        },
        {
          value: 1,
          label: '禁用'
        }
      ],
      visible: false,
      dataForm: {
        id: '',
        postType: '',
        title: '',
        content: '',
        thumbnail: '',
        isDelete: ''
      },
      dataRule: {
        postType: [
            { required: true, message: '标签不能为空', trigger: 'blur' }
        ],
        title: [
            { required: true, message: '标题不能为空', trigger: 'blur' }
        ],
        thumbnail: [
            { required: true, message: '缩略图不能为空', trigger: 'blur' }
        ],
        content: [
            { required: true, message: '内容不能为空', trigger: 'blur' }
        ],
        isDelete: [
            { required: true, message: '状态不能为空', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    init (row) {
      this.url = this.$http.adornUrl(`/sys/oss/upload?token=${this.$cookie.get('token')}`)
      this.visible = true
      this.$nextTick(() => {
        if (row) {
          this.dataForm = row
        } else {
          this.dataForm = {}
        }
      })
    },
    // 表单提交
    dataFormSubmit () {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(`/manager/officialPost/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'postType': this.dataForm.postType,
              'title': this.dataForm.title,
              'thumbnail': this.dataForm.thumbnail,
              'content': this.dataForm.content,
              'isDelete': this.dataForm.isDelete
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
    }
  }
}
</script>