<template>
  <el-dialog
    :title="!dataForm.userId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="dataForm.username" placeholder="用户名" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="用户昵称" prop="nickname">
          <el-input v-model="dataForm.nickname" placeholder="用户昵称" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="手机号" prop="mobile">
          <el-input v-model="dataForm.mobile" placeholder="手机号" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="账号状态" prop="status">
          <el-radio-group v-model="dataForm.status">
            <el-radio :label="1">正常</el-radio>
            <el-radio :label="0">禁用</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col>
    </el-row>

    <!-- <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="密码" prop="password" :class="{ 'is-required': !dataForm.userId }">
          <el-input v-model="dataForm.password" type="password" placeholder="密码" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="确认密码" prop="comfirmPassword" :class="{ 'is-required': !dataForm.userId }">
          <el-input v-model="dataForm.comfirmPassword" type="password" placeholder="确认密码" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
    </el-row> -->

    <el-row type="flex">
      <!-- <el-col :span="11">
        <el-form-item label="用户身份" prop="roleType" required >
          <el-radio-group v-model="dataForm.roleType">
            <el-radio :label="1">普通用户</el-radio>
            <el-radio :label="2">上游商户</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col> -->
      <!-- <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="注册来源" prop="source" >
          <el-radio-group v-model="dataForm.source">
            <el-radio :label="1">微信</el-radio>
            <el-radio :label="2">手机用户</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col> -->

 <!-- 1.手机号 + 验证码 2.微信 3.appleId 4.手机号 + 密码 -->
      <el-form-item label="注册来源" prop="source" >
          <el-radio-group v-model="dataForm.source">
            <el-radio :label="1">手机号 + 验证码</el-radio>
            <el-radio :label="2">微信</el-radio>
            <el-radio :label="3">AppleId</el-radio>
            <el-radio :label="4">手机号 + 密码</el-radio>
          </el-radio-group>
        </el-form-item>
    </el-row>
    <el-form-item
        label="用户头像"
        prop="avatar">
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
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="所在城市" prop="city">
          <el-input v-model="dataForm.city" placeholder="所在城市" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="性别" prop="gender">
          <el-radio-group v-model="dataForm.gender">
            <el-radio :label=null>未知</el-radio>
            <el-radio :label="1">男</el-radio>
            <el-radio :label="2">女</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col>
    </el-row>
    <template v-if="dataForm.userId">
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
          userId: 0,
          username: '',
          mobile: '',
          nickname: '',
          password: '',
          avatar: '',
          status: 1,
          gender: 1,
          // roleType: 1,
          source: 1,
          city: '',
          isIdentify: '',
          isJmessage: '',
          wxOpenId: '',
          wxUnionId: '',
          thirdPartyId: '',
          createTime: '',
          modifyTime: ''
        },
        dataRule: {
          username: [
            { required: true, message: '用户名不能为空', trigger: 'blur' }
          ],
          nickname: [
            { required: true, message: '昵称不能为空', trigger: 'blur' }
          ],
          mobile: [
            { required: true, message: '手机号不能为空', trigger: 'blur' },
            { validator: validateMobile, trigger: 'blur' }
          ],
          status: [
            { required: true, message: '账号状态不能为空', trigger: 'blur' }
          ],
          // roleType: [
          //   { required: true, message: '用户身份不能为空', trigger: 'blur' }
          // ],
          source: [
            { required: true, message: '注册来源不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.url = this.$http.adornUrl(`/sys/oss/upload?token=${this.$cookie.get('token')}`)
        this.dataForm.userId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.userId) {
            this.dataForm.fileList = []
            this.$http({
              url: this.$http.adornUrl(`/manager/user/info/${this.dataForm.userId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.username = data.user.username
                this.dataForm.mobile = data.user.mobile
                this.dataForm.nickname = data.user.nickname
                this.dataForm.password = data.user.password
                this.dataForm.avatar = data.user.avatar
                this.dataForm.status = data.user.status
                this.dataForm.gender = data.user.gender
                // this.dataForm.roleType = data.user.roleType
                this.dataForm.source = data.user.source
                this.dataForm.city = data.user.city
                this.dataForm.isIdentify = data.user.isIdentify
                this.dataForm.isJmessage = data.user.isJmessage
                this.dataForm.wxOpenId = data.user.wxOpenId
                this.dataForm.wxUnionId = data.user.wxUnionId
                this.dataForm.thirdPartyId = data.user.thirdPartyId
                this.dataForm.createTime = data.user.createTime
                this.dataForm.modifyTime = data.user.modifyTime
                if (data.user.avatar != null) {
                  this.dataForm.fileList.push({
                    name: 'image',
                    url: data.user.avatar
                  })
                }
              }
            })
          } else {
            this.dataForm.fileList = []
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/manager/user/${!this.dataForm.userId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'userId': this.dataForm.userId || undefined,
                'username': this.dataForm.username,
                'mobile': this.dataForm.mobile,
                'nickname': this.dataForm.nickname,
                'password': this.dataForm.password,
                'avatar': this.dataForm.avatar,
                'status': this.dataForm.status,
                'gender': this.dataForm.gender,
                // 'roleType': this.dataForm.roleType,
                'source': this.dataForm.source,
                'city': this.dataForm.city,
                'isIdentify': this.dataForm.isIdentify,
                'isJmessage': this.dataForm.isJmessage,
                'wxOpenId': this.dataForm.wxOpenId,
                'wxUnionId': this.dataForm.wxUnionId,
                'thirdPartyId': this.dataForm.thirdPartyId,
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
        fileList.map(item => {
          if (file.uid === item.uid) {
            this.dataForm.fileList.remove({
              name: item.name,
              url: item.url
            })
          }
        })
      },
      handleSuccess (res, file, fileList) {
        this.dataForm.fileList.push({
          name: file.name,
          url: res.url
        })
        this.dataForm.avatar = res.url
      },
      handleExceed (files, fileList) {
        alert('文件超出个数限制!')
      }
    }
  }
</script>
