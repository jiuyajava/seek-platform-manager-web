<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    
    <el-form-item label="景点名称" prop="title">
      <el-input v-model="dataForm.title" placeholder="景点名称" style="width:92%"></el-input>
    </el-form-item>

    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="线路ID" prop="lineId">
          <el-input v-model="dataForm.lineId" placeholder="线路ID" style="width: 92%"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="里程(km)" prop="distance">
          <el-input v-model="dataForm.distance" placeholder="全程距离(km)" style="width: 80%"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="所在城市" prop="city">
           <el-input v-model="dataForm.city" placeholder="所在城市" style="width: 92%"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="详细地址" prop="address">
          <el-input v-model="dataForm.address" placeholder="详细地址" style="width: 80%"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-form-item
        label="景点小图"
        prop="thumbnail">
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
    <el-form-item
        label="景点图片"
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
    <el-form-item label="景点描述" prop="description">
      <el-input type="textarea" v-model="dataForm.description" placeholder="景点描述" :autosize="{ minRows: 3, maxRows: 8}" style="width: 92%"></el-input>
    </el-form-item>
    <el-row type="flex">
      <el-form-item label="最佳月份" prop="bestMonths">
        <el-checkbox-group v-model="dataForm.checkedMonths" @change="changeMonth" >
          <el-checkbox v-for="month in dataForm.months" :label="month" :key="month">{{month}}</el-checkbox>
        </el-checkbox-group>
      </el-form-item>
    </el-row>  
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="行程顺序" prop="orderNum">
          <el-input v-model="dataForm.orderNum" placeholder="行程顺序" style="width: 80%"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="行程提示" prop="tips">
          <el-input v-model="dataForm.tips" placeholder="下个景点提示" style="width: 80%"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="难度" prop="hardLevel">
          <el-input v-model="dataForm.hardLevel" placeholder="难度" style="width: 80%"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="玩法" prop="playWay">
          <el-input v-model="dataForm.playWay" placeholder="玩法" style="width: 80%"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="地形" prop="geography">
          <el-input v-model="dataForm.geography" placeholder="地形" style="width: 80%"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="耗时" prop="takeTime">
          <el-input v-model="dataForm.takeTime" placeholder="耗时" style="width: 80%"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-form-item label="标签/话题" prop="tags">
      <el-input v-model="dataForm.tags" placeholder="标签/话题" style="width: 92%"></el-input>
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
  const monthOptions = ['1', '2', '3', '4', '5', '6', '7', '8', '9', '10', '11', '12']
  export default {
    data () {
      return {
        url: '',
        fileList: [],
        visible: false,
        dataForm: {
          checkedMonths: ['1', '3'],
          months: monthOptions,
          id: 0,
          lineId: '',
          labelType: 1,
          thumbnail: '',
          images: '',
          title: '',
          description: '',
          bestMonths: '',
          playWay: '',
          hardLevel: '',
          takeTime: '',
          geography: '',
          distance: '',
          city: '',
          address: '',
          tips: '',
          orderNum: '',
          createTime: '',
          modifyTime: ''
        },
        dataRule: {
          lineId: [
            { required: true, message: '线路ID不能为空', trigger: 'blur' }
          ],
          thumbnail: [
            { required: true, message: '景点简介缩略图不能为空', trigger: 'blur' }
          ],
          images: [
            { required: true, message: '景点详情图片集合不能为空', trigger: 'blur' }
          ],
          title: [
            { required: true, message: '景点名称不能为空', trigger: 'blur' }
          ],
          description: [
            { required: true, message: '景点描述不能为空', trigger: 'blur' }
          ],
          bestMonths: [
            { required: true, message: '最佳月份不能为空', trigger: 'blur' }
          ],
          distance: [
            { required: true, message: '全程距离(km)不能为空', trigger: 'blur' }
          ],
          city: [
            { required: true, message: '所在城市不能为空', trigger: 'blur' }
          ],
          address: [
            { required: true, message: '详细地址不能为空', trigger: 'blur' }
          ],
          orderNum: [
            { required: true, message: '排序不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.url = this.$http.adornUrl(`/sys/oss/upload?token=${this.$cookie.get('token')}`)
        this.fileList = []
        this.dataForm.checkedMonths = []
        this.dataForm.thumbnail = null
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/manager/linedetails/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.lineId = data.lineDetails.lineId
                this.dataForm.thumbnail = data.lineDetails.thumbnail
                this.dataForm.images = data.lineDetails.images
                this.dataForm.title = data.lineDetails.title
                this.dataForm.description = data.lineDetails.description
                this.dataForm.bestMonths = data.lineDetails.bestMonths
                this.dataForm.distance = data.lineDetails.distance
                this.dataForm.city = data.lineDetails.city
                this.dataForm.address = data.lineDetails.address
                this.dataForm.tips = data.lineDetails.tips
                this.dataForm.orderNum = data.lineDetails.orderNum
                this.dataForm.playWay = data.lineDetails.playWay
                this.dataForm.hardLevel = data.lineDetails.hardLevel
                this.dataForm.takeTime = data.lineDetails.takeTime
                this.dataForm.geography = data.lineDetails.geography
                this.dataForm.tags = data.lineDetails.tags
                this.dataForm.createTime = data.lineDetails.createTime
                this.dataForm.modifyTime = data.lineDetails.modifyTime
                this.initCheckedMonths()
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
              url: this.$http.adornUrl(`/manager/linedetails/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'lineId': this.dataForm.lineId,
                'thumbnail': this.dataForm.thumbnail,
                'images': this.dataForm.images,
                'title': this.dataForm.title,
                'description': this.dataForm.description,
                'bestMonths': this.dataForm.bestMonths,
                'distance': this.dataForm.distance,
                'city': this.dataForm.city,
                'address': this.dataForm.address,
                'tips': this.dataForm.tips,
                'playWay': this.dataForm.playWay,
                'hardLevel': this.dataForm.hardLevel,
                'takeTime': this.dataForm.takeTime,
                'geography': this.dataForm.geography,
                'tags': this.dataForm.tags,
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
      changeMonth (val) {
        this.dataForm.bestMonths = val.join(',')
      },
      initCheckedMonths () {
        if (this.dataForm.bestMonths !== null && this.dataForm !== '') {
          this.dataForm.checkedMonths = this.dataForm.bestMonths.split(',')
        }
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
