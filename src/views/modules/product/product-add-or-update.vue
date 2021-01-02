<template>
  <el-dialog
    :title="!dataForm.packageId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" label-width="80px">
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="套餐名称" prop="packageName">
          <el-input v-model="dataForm.packageName" placeholder="套餐名称" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="供应商ID" prop="supplierId">
          <el-input v-model="dataForm.supplierId" placeholder="供应商ID" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="所属城市" prop="cityName">
          <el-input v-model="dataForm.cityName" placeholder="所属城市" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="城市编码" prop="cityCode">
          <el-input v-model="dataForm.cityCode" placeholder="城市编码" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-form-item label="详细地址" prop="address">
      <el-input v-model="dataForm.address" placeholder="详细地址" style="width: 92%;" ></el-input>
    </el-form-item>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="售卖状态" prop="status">
          <el-radio-group v-model="dataForm.status">
            <el-radio :label="1">上架</el-radio>
            <el-radio :label="2">下架</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="地理坐标" prop="longitudeLatitude">
          <el-input v-model="dataForm.longitudeLatitude" placeholder="地理坐标经纬度" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="商品编号" prop="packageCode">
          <el-input v-model="dataForm.packageCode" placeholder="商品编号" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="展示价格" prop="salesPrice">
          <el-input v-model="dataForm.salesPrice" placeholder="展示价格" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-form-item
        label="缩率图"
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
      label="套餐图片"
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
    <el-form-item label="套餐简述" prop="introduces">
      <el-input type="textarea" v-model="dataForm.introduces" placeholder="套餐简述" :autosize="{ minRows: 3, maxRows: 5}" style="width: 92%"></el-input>
    </el-form-item>
    <el-form-item label="套餐详情" prop="details">
      <jos-editor ref="editor" 
        v-model="dataForm.details"
        @onClick="previewContent"
        style="width: 92%;"/>
    </el-form-item>
    <el-form-item label="备注信息" prop="remark">
      <el-input type="textarea" v-model="dataForm.remark" placeholder="备注信息" :autosize="{ minRows: 3, maxRows: 8}" style="width: 92%"></el-input>
    </el-form-item>
    <template v-if="dataForm.packageId">
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
  .tox-tinymce-aux {
    z-index: 3000 !important;
  }
</style>

<script>
  import JosEditor from '@/components/tinymce-editor'
  import { isPrice } from '@/utils/validate'
  export default {
    components: {
      JosEditor
    },
    data () {
      var validateSalesPrice = (rule, value, callback) => {
        if (!isPrice(value)) {
          callback(new Error('展示价格错误'))
        } else {
          callback()
        }
      }
      return {
        url: '',
        fileList: [],
        visible: false,
        dataForm: {
          packageId: 0,
          packageName: '',
          packageCode: '',
          supplierId: '',
          status: 2,
          introduces: '',
          details: '',
          salesPrice: '',
          thumbnail: '',
          images: '',
          agent: '',
          cityName: '',
          cityCode: '',
          address: '',
          longitudeLatitude: '',
          remark: '',
          isDel: false,
          createTime: '',
          modifyTime: ''
        },
        dataRule: {
          salesPrice: [
            { required: true, message: '展示价格不能为空', trigger: 'blur' },
            { validator: validateSalesPrice, trigger: 'blur' }
          ],
          packageName: [
            { required: true, message: '商品名称不能为空', trigger: 'blur' }
          ],
          supplierId: [
            { required: true, message: '供应商ID不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '售卖状态不能为空', trigger: 'blur' }
          ],
          introduces: [
            { required: true, message: '套餐简述不能为空', trigger: 'blur' }
          ],
          details: [
            { required: true, message: '套餐详情不能为空', trigger: 'blur' }
          ],
          thumbnail: [
            { required: true, message: '缩略图不能为空', trigger: 'blur' }
          ],
          images: [
            { required: true, message: '套餐图片不能为空', trigger: 'blur' }
          ],
          cityName: [
            { required: true, message: '所属城市不能为空', trigger: 'blur' }
          ],
          cityCode: [
            { required: true, message: '城市编码不能为空', trigger: 'blur' }
          ],
          address: [
            { required: true, message: '详细地址不能为空', trigger: 'blur' }
          ],
          longitudeLatitude: [
            { required: true, message: '地理坐标不能为空', trigger: 'blur' }
          ],
          isDel: [
            { required: true, message: '是否删除不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      previewContent (e, editor) {
        console.log(this.dataForm.details)
      },
      init (packageId) {
        this.url = this.$http.adornUrl(`/sys/oss/upload?token=${this.$cookie.get('token')}`)
        this.fileList = []
        this.dataForm.thumbnail = null
        this.dataForm.packageId = packageId || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.packageId) {
            this.$http({
              url: this.$http.adornUrl(`/manager/product/info/${this.dataForm.packageId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.packageId = data.product.packageId
                this.dataForm.packageName = data.product.packageName
                this.dataForm.packageCode = data.product.packageCode
                this.dataForm.supplierId = data.product.supplierId
                this.dataForm.status = data.product.status
                this.dataForm.introduces = data.product.introduces
                this.dataForm.details = data.product.details
                this.dataForm.salesPrice = data.product.salesPrice
                this.dataForm.thumbnail = data.product.thumbnail
                this.dataForm.images = data.product.images
                this.dataForm.agent = data.product.agent
                this.dataForm.cityName = data.product.cityName
                this.dataForm.cityCode = data.product.cityCode
                this.dataForm.address = data.product.address
                this.dataForm.longitudeLatitude = data.product.longitudeLatitude
                this.dataForm.remark = data.product.remark
                this.dataForm.isDel = data.product.isDel
                this.dataForm.createTime = data.product.createTime
                this.dataForm.modifyTime = data.product.modifyTime
                if (this.dataForm.images != null && this.dataForm.images.length > 0) {
                  this.dataForm.images.split(',').map((url, i) => {
                    this.fileList.push({
                      name: '图片' + (i + 1),
                      url: url
                    })
                  })
                }
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
              url: this.$http.adornUrl(`/manager/product/${!this.dataForm.packageId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'packageId': this.dataForm.packageId || undefined,
                'packageName': this.dataForm.packageName,
                'packageCode': this.dataForm.packageCode,
                'supplierId': this.dataForm.supplierId,
                'status': this.dataForm.status,
                'introduces': this.dataForm.introduces,
                'details': this.dataForm.details,
                'salesPrice': this.dataForm.salesPrice,
                'thumbnail': this.dataForm.thumbnail,
                'images': this.dataForm.images,
                'agent': this.dataForm.agent,
                'cityName': this.dataForm.cityName,
                'cityCode': this.dataForm.cityCode,
                'address': this.dataForm.address,
                'longitudeLatitude': this.dataForm.longitudeLatitude,
                'remark': this.dataForm.remark,
                'isDel': this.dataForm.isDel,
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
