<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="跳转类型" prop="linkType">
          <el-radio-group v-model="dataForm.linkType">
            <el-radio :label="1">商品</el-radio>
            <el-radio :label="2">帖子</el-radio>
            <el-radio :label="3">网址</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="跳转参数" prop="params">
      <el-input v-model="dataForm.params" placeholder="跳转参数"></el-input>
    </el-form-item>
      </el-col>
    </el-row>
    <el-form-item
        label="轮播图"
        prop="image">
      <el-upload
        class="avatar-uploader"
        :action="url"
        :show-file-list="false"
        :on-success="handleThumbnailSuccess"
        :on-remove="handleThumbnailRemove"
        :before-upload="beforeUpload">
        <img v-if="dataForm.image" :src="dataForm.image" class="avatar">
        <i v-else class="el-icon-plus avatar-uploader-icon"></i>
      </el-upload>
    </el-form-item>
    <el-form-item label="排序" prop="orderNum">
      <el-input v-model="dataForm.orderNum" placeholder="排序"></el-input>
    </el-form-item>
    <el-form-item label="标题" prop="title">
      <el-input v-model="dataForm.title" placeholder="标题"></el-input>
    </el-form-item>
    <template v-if="dataForm.id">
      <el-row type="flex">
        <el-col :span="11">
          <el-form-item label="创建时间" prop="createTime">
            <el-date-picker v-model="dataForm.createTime" type="datetime" placeholder="创建时间" style="width: 80%;" readonly></el-date-picker>
          </el-form-item>
        </el-col>
        <el-col class="line" :span="2">-</el-col>
        <el-col :span="11">
          <el-form-item label="修改时间" prop="modifyTime">
            <el-date-picker v-model="dataForm.modifyTime" type="datetime" placeholder="修改时间" style="width: 80%;" readonly></el-date-picker>
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
</style>

<script>
  export default {
    data () {
      return {
        url: '',
        visible: false,
        dataForm: {
          id: 0,
          image: '',
          title: '',
          linkType: '',
          params: '',
          orderNum: '',
          createTime: '',
          modifyTime: ''
        },
        dataRule: {
          image: [
            { required: true, message: '图片不能为空', trigger: 'blur' }
          ],
          linkType: [
            { required: true, message: '跳转类型不能为空', trigger: 'blur' }
          ],
          params: [
            { required: true, message: '跳转参数不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.url = this.$http.adornUrl(`/sys/oss/upload?token=${this.$cookie.get('token')}`)
        this.dataForm.image = null
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/manager/banner/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.image = data.banner.image
                this.dataForm.title = data.banner.title
                this.dataForm.linkType = data.banner.linkType
                this.dataForm.params = data.banner.params
                this.dataForm.orderNum = data.banner.orderNum
                this.dataForm.createTime = data.banner.createTime
                this.dataForm.modifyTime = data.banner.modifyTime
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
              url: this.$http.adornUrl(`/manager/banner/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'image': this.dataForm.image,
                'title': this.dataForm.title,
                'linkType': this.dataForm.linkType,
                'params': this.dataForm.params,
                'orderNum': this.dataForm.orderNum,
                'createTime': this.dataForm.createTime,
                'modifyTime': this.dataForm.modifyTime
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
          this.dataForm.image = res.url
        } else {
          this.$message.error('网络错误，上传失败，请重试！')
        }
      },
      handleThumbnailRemove () {
        this.dataForm.image = null
      }
    }
  }
</script>
