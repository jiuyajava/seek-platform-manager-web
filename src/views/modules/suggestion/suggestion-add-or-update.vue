<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="内容描述" prop="content">
      <el-input type="textarea" v-model="dataForm.content" placeholder="内容描述" :autosize="{ minRows: 3, maxRows: 8}" readonly style="width: 92%"></el-input>
    </el-form-item>
    <el-form-item
        label="反馈图片"
        prop="images">
        <el-upload
          drag
          name="file"
          list-type="picture"
          :limit=3
          :action="url"
          :file-list="fileList"
          :on-success="handleSuccess"
          :on-remove="handleRemove"
          :on-exceed="handleExceed"
          style="width: 92%" >
          <i class="el-icon-upload"></i>
          <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
          <div class="el-upload__tip" slot="tip">只能上传只支持jpg、png、gif格式的文件，最多上传三张，且不超过2MB</div>
        </el-upload>
    </el-form-item>
    <el-form-item label="结果备注" prop="remark">
      <el-input type="textarea" v-model="dataForm.remark" placeholder="处理结果备注" :autosize="{ minRows: 3, maxRows: 8}" style="width: 92%"></el-input>
    </el-form-item>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="用户ID" prop="userId">
          <el-input v-model="dataForm.userId" placeholder="用户ID" readonly style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="处理状态" prop="status">
          <el-radio-group v-model="dataForm.status">
            <el-radio :label="1">待处理</el-radio>
            <el-radio :label="2">已处理</el-radio>
          </el-radio-group>
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

<script>
  export default {
    data () {
      return {
        url: '',
        fileList: [],
        visible: false,
        dataForm: {
          id: 0,
          userId: '',
          content: '',
          images: '',
          remark: '',
          status: '',
          createTime: '',
          modifyTime: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户id不能为空', trigger: 'blur' }
          ],
          content: [
            { required: true, message: '内容描述不能为空', trigger: 'blur' }
          ],
          images: [
            { required: true, message: '投诉举证图片不能为空', trigger: 'blur' }
          ],
          remark: [
            { required: true, message: '处理结果备注不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '处理状态:   1、待处理 2、已处理不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.url = this.$http.adornUrl(`/sys/oss/upload?token=${this.$cookie.get('token')}`)
        this.fileList = []
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/manager/suggestion/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.suggestion.userId
                this.dataForm.content = data.suggestion.content
                this.dataForm.images = data.suggestion.images
                this.dataForm.remark = data.suggestion.remark
                this.dataForm.status = data.suggestion.status
                this.dataForm.createTime = data.suggestion.createTime
                this.dataForm.modifyTime = data.suggestion.modifyTime
                if (this.dataForm.images != null && this.dataForm.images.length > 0) {
                  this.dataForm.images.split(',').map((url, i) => {
                    this.fileList.push({
                      name: '图片' + (i + 1),
                      url: url
                    })
                  })
                }
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
              url: this.$http.adornUrl(`/manager/suggestion/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'content': this.dataForm.content,
                'images': this.dataForm.images,
                'remark': this.dataForm.remark,
                'status': this.dataForm.status,
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
      handleRemove (file, fileList) {
        if (fileList.length > 0) {
          this.dataForm.images = fileList.map(item => {
            return item.url
          }).join(',')
          this.fileList = fileList
        }
      },
      handleSuccess (res, file, fileList) {
        this.fileList.push({
          name: file.name,
          url: res.url
        })
        if (fileList.length > 0) {
          this.dataForm.images = fileList.map(item => {
            return item.url
          }).join(',')
        }
      },
      handleExceed (files, fileList) {
        alert('文件超出个数限制!')
      }
    }
  }
</script>
