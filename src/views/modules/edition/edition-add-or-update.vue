<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
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
          ><div class="grid-content bg-purple-light">
            <el-form-item label="客户端">
              <el-select v-model="dataForm.client" >
                <el-option
                  v-for="item in clientOptions"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                >
                </el-option>
              </el-select>
            </el-form-item></div
        ></el-col>
      </el-row>
        
    
      <el-row v-if="dataForm.client === 1">
          <el-col :span="8"
          ><div class="grid-content bg-purple-light">
            <el-form-item label="平台类型">
              <el-input v-model="dataForm.type" ></el-input>
            </el-form-item></div
        ></el-col>
          <el-col :span="8"
          ><div class="grid-content bg-purple-light">
            <el-form-item label="类型描述">
              <el-input v-model="dataForm.typeStr" ></el-input>
            </el-form-item></div
        ></el-col>
      </el-row>

       <el-row>
        <el-col :span="8"
                  ><div class="grid-content bg-purple-light">
                    <el-form-item label="版本">
                      <el-input v-model="dataForm.edition" ></el-input>
                    </el-form-item></div
                ></el-col>
                 <el-col :span="8">
        <el-form-item label="状态" prop="status">
          <el-radio-group v-model="dataForm.status">
            <el-radio :label="1">启用</el-radio>
            <el-radio :label="0">禁用</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col>
        </el-row>

       <el-row>
        <el-col :span="8"
                  ><div class="grid-content bg-purple-light">
                    <el-form-item label="链接">
                      <el-input v-model="dataForm.link" ></el-input>
                    </el-form-item></div
                ></el-col>
                
        </el-row>
       <el-row>
        <el-col>
          <el-form-item label="描述">
            <el-input type="textarea" :rows="2" v-model="dataForm.content">
            </el-input>
          </el-form-item>
        </el-col>
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
      clientOptions: [
        {
          value: 2,
          label: 'iOS'
        },
        {
          value: 1,
          label: 'Andorid'
        }
      ],
      visible: false,
      dataForm: {
        id: '',
        client: '',
        type: '',
        typeStr: '',
        edition: '',
        status: ''
      },
      dataRule: {
        client: [
            { required: true, message: '标签不能为空', trigger: 'blur' }
        ],
        status: [
            { required: true, message: '标题不能为空', trigger: 'blur' }
        ],
        edition: [
            { required: true, message: '缩略图不能为空', trigger: 'blur' }
        ],
        link: [
            { required: true, message: '链接不能为空', trigger: 'blur' }
        ],
        content: [
            { required: true, message: '内容不能为空', trigger: 'blur' }
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
            url: this.$http.adornUrl(`/manager/edition/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'client': this.dataForm.client,
              'type': this.dataForm.type,
              'typeStr': this.dataForm.typeStr,
              'content': this.dataForm.content,
              'edition': this.dataForm.edition,
              'link': this.dataForm.link,
              'status': this.dataForm.status
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
    }
  }
}
</script>