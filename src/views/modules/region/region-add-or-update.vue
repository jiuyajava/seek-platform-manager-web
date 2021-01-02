<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="名称" prop="name">
          <el-input v-model="dataForm.name" placeholder="名称" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="行政编码" prop="code">
          <el-input v-model="dataForm.code" placeholder="行政编码" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="等级" prop="leavel">
          <el-input v-model="dataForm.leavel" placeholder="等级" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="父Id" prop="parentId">
          <el-input v-model="dataForm.parentId" placeholder="父Id" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="首字母" prop="tag">
          <el-input v-model="dataForm.tag" placeholder="首字母" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="热门城市" prop="isHot">
          <el-radio-group v-model="dataForm.isHot">
            <el-radio :label="true">是</el-radio> 
            <el-radio :label="false">否</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col>
    </el-row>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="区号" prop="areaCode">
          <el-input v-model="dataForm.areaCode" placeholder="区号" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="经纬度" prop="longitudeLatitude">
          <el-input v-model="dataForm.longitudeLatitude" placeholder="经纬度" style="width: 80%;"></el-input>
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
        visible: false,
        dataForm: {
          id: 0,
          code: '',
          name: '',
          leavel: '',
          areaCode: '',
          longitudeLatitude: '',
          parentId: '',
          tag: '',
          isHot: false,
          createTime: '',
          modifyTime: ''
        },
        dataRule: {
          code: [
            { required: true, message: '行政编码不能为空', trigger: 'blur' }
          ],
          name: [
            { required: true, message: '名称不能为空', trigger: 'blur' }
          ],
          leavel: [
            { required: true, message: '等级不能为空', trigger: 'blur' }
          ],
          areaCode: [
            { required: true, message: '邮编不能为空', trigger: 'blur' }
          ],
          isHot: [
            { required: true, message: '请选择是否为热门城市', trigger: 'blur' }
          ],
          // longitudeLatitude: [
          //   { required: true, message: '经纬度不能为空', trigger: 'blur' }
          // ],
          parentId: [
            { required: true, message: '父Id不能为空', trigger: 'blur' }
          ],
          tag: [
            { required: true, message: '首字母不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/manager/region/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.code = data.region.code
                this.dataForm.name = data.region.name
                this.dataForm.leavel = data.region.leavel
                this.dataForm.areaCode = data.region.areaCode
                this.dataForm.longitudeLatitude = data.region.longitudeLatitude
                this.dataForm.parentId = data.region.parentId
                this.dataForm.tag = data.region.tag
                this.dataForm.isHot = data.region.isHot
                this.dataForm.createTime = data.region.createTime
                this.dataForm.modifyTime = data.region.modifyTime
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
              url: this.$http.adornUrl(`/manager/region/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'code': this.dataForm.code,
                'name': this.dataForm.name,
                'leavel': this.dataForm.leavel,
                'areaCode': this.dataForm.areaCode,
                'longitudeLatitude': this.dataForm.longitudeLatitude,
                'parentId': this.dataForm.parentId,
                'tag': this.dataForm.tag,
                'isHot': this.dataForm.isHot,
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
      }
    }
  }
</script>
