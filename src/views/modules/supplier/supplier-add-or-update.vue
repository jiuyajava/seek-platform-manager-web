<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="名称" prop="name">
          <el-input v-model="dataForm.name" placeholder="供应商名称" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="编码" prop="code">
          <el-input v-model="dataForm.code" placeholder="供应商编码" style="width: 80%"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="手机号" prop="mobile">
          <el-input v-model="dataForm.mobile" placeholder="供应商手机号" style="width: 80%"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="座机" prop="tel">
          <el-input v-model="dataForm.tel" placeholder="供应商固话" style="width: 80%"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-form-item
        label="营业执照"
        prop="businessLicense">
        <el-upload
          drag
          name="file"
          list-type="picture"
          :limit=1
          :action="url"
          :file-list="dataForm.fileList"
          :on-success="handleSuccess"
          :on-remove="handleRemove"
          :on-exceed="handleExceed">
          <i class="el-icon-upload"></i>
          <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
          <div class="el-upload__tip" slot="tip">只能上传只支持jpg、png、gif格式的文件，最多上传一张，且不超过2MB</div>
        </el-upload>

    </el-form-item>
    <el-form-item label="营业地址" prop="address">
      <el-input v-model="dataForm.address" placeholder="营业地址"></el-input>
    </el-form-item>
    <!-- <el-form-item
        label="商户Logo"
        prop="logo">
        <el-upload
          drag
          name="file"
          list-type="picture"
          :limit=1
          :action="url"
          :file-list="dataForm.fileList"
          :on-success="handleSuccess"
          :on-remove="handleRemove"
          :on-exceed="handleExceed">
          <i class="el-icon-upload"></i>
          <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
          <div class="el-upload__tip" slot="tip">只能上传只支持jpg、png、gif格式的文件，最多上传一张，且不超过2MB</div>
        </el-upload>
    </el-form-item> -->
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
  import { isMobile } from '@/utils/validate'
  export default {
    data () {
      var validateMobile = (rule, value, callback) => {
        if (!isMobile(value)) {
          callback(new Error('手机号格式错误'))
        } else {
          callback()
        }
      }
      return {
        url: '',
        visible: false,
        dataForm: {
          fileList: [],
          id: 0,
          name: '',
          code: '',
          mobile: '',
          tel: '',
          // logo: '',
          address: '',
          businessLicense: '',
          createTime: '',
          modifyTime: ''
        },
        dataRule: {
          name: [
            { required: true, message: '供应商名称不能为空', trigger: 'blur' }
          ],
          code: [
            { required: true, message: '供应商编码不能为空', trigger: 'blur' }
          ],
          mobile: [
            { required: true, message: '供应商手机号不能为空', trigger: 'blur' },
            { validator: validateMobile, trigger: 'blur' }
          ],
          businessLicense: [
             { required: true, message: '营业执照不能为空', trigger: 'change' }
          ],
          address: [
            { required: true, message: '营业地址不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.url = this.$http.adornUrl(`/sys/oss/upload?token=${this.$cookie.get('token')}`)
        this.dataForm.id = id || 0
        this.dataForm.fileList = []
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/manager/supplier/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.name = data.supplier.name
                this.dataForm.code = data.supplier.code
                this.dataForm.mobile = data.supplier.mobile
                this.dataForm.tel = data.supplier.tel
                // this.dataForm.logo = data.supplier.logo
                this.dataForm.address = data.supplier.address
                this.dataForm.createTime = data.supplier.createTime
                this.dataForm.modifyTime = data.supplier.modifyTime
                this.dataForm.businessLicense = data.supplier.businessLicense
                if (data.supplier.businessLicense != null) {
                  this.dataForm.fileList.push({
                    name: 'image',
                    url: data.supplier.businessLicense
                  })
                }
              } else {
                this.dataForm.fileList = []
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
              url: this.$http.adornUrl(`/manager/supplier/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'name': this.dataForm.name,
                'code': this.dataForm.code,
                'mobile': this.dataForm.mobile,
                'tel': this.dataForm.tel,
                'address': this.dataForm.address,
                'businessLicense': this.dataForm.businessLicense,
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
        this.dataForm.fileList = []
        this.dataForm.businessLicense = null
      },
      handleSuccess (res, file, fileList) {
        console.log(fileList)
        this.dataForm.fileList.push({
          name: file.name,
          url: res.url
        })
        this.dataForm.businessLicense = res.url
      },
      handleExceed (files, fileList) {
        alert('文件超出个数限制!')
      }
    }
  }
</script>
