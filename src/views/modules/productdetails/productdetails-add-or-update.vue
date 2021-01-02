<template>
  <el-dialog
    :title="!dataForm.productId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="商品名称" prop="productName">
          <el-input v-model="dataForm.productName" placeholder="商品名称" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="售卖类型" prop="saleType">
          <el-radio-group v-model="dataForm.saleType">
            <el-radio :label="1">全款商品</el-radio>
            <el-radio :label="2">订金商品</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col>
    </el-row>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="销售价" prop="salesPrice">
          <el-input v-model="dataForm.salesPrice" placeholder="销售价" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="订金价格" prop="depositPrice">
          <el-input v-model="dataForm.depositPrice" placeholder="订金价格" style="width: 80%;"></el-input>
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
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="套餐ID" prop="packageId">
          <el-input v-model="dataForm.packageId" placeholder="套餐ID" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="库存数量" prop="stock">
          <el-input v-model="dataForm.stock" placeholder="库存数量" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <template v-if="dataForm.productId">
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
  import { isNumber, isPrice } from '@/utils/validate'
  export default {
    data () {
      var validateDepositPrice = (rule, value, callback) => {
        if (!isPrice(value)) {
          callback(new Error('订金价格错误'))
        } else {
          callback()
        }
      }
      var validateSalesPrice = (rule, value, callback) => {
        if (!isPrice(value)) {
          callback(new Error('销售价格错误'))
        } else {
          callback()
        }
      }
      var validateNumber = (rule, value, callback) => {
        if (!isNumber(value)) {
          callback(new Error('库存数量错误'))
        } else {
          callback()
        }
      }
      return {
        url: '',
        visible: false,
        dataForm: {
          productId: 0,
          productName: '',
          packageId: '',
          saleType: 1,
          depositPrice: '',
          salesPrice: '',
          thumbnail: '',
          stock: 0,
          createTime: '',
          modifyTime: ''
        },
        dataRule: {
          packageId: [
            { required: true, message: '套餐ID不能为空', trigger: 'blur' }
          ],
          productName: [
            { required: true, message: '套餐名称不能为空', trigger: 'blur' }
          ],
          saleType: [
            { required: true, message: '售卖类型不能为空', trigger: 'blur' }
          ],
          depositPrice: [
            { required: true, message: '订金价格不能为空', trigger: 'blur' },
            { validator: validateDepositPrice, trigger: 'blur' }
          ],
          salesPrice: [
            { required: true, message: '销售价不能为空', trigger: 'blur' },
            { validator: validateSalesPrice, trigger: 'blur' }
          ],
          thumbnail: [
            { required: true, message: '商品图片不能为空', trigger: 'blur' }
          ],
          stock: [
            { required: true, message: '库存数量不能为空', trigger: 'blur' },
            { validator: validateNumber, trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (productId) {
        this.dataForm.productId = productId || 0
        this.url = this.$http.adornUrl(`/sys/oss/upload?token=${this.$cookie.get('token')}`)
        this.dataForm.thumbnail = null
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.productId) {
            this.$http({
              url: this.$http.adornUrl(`/manager/productdetails/info/${this.dataForm.productId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.productId = data.productDetails.productId
                this.dataForm.productName = data.productDetails.productName
                this.dataForm.packageId = data.productDetails.packageId
                this.dataForm.saleType = data.productDetails.saleType
                this.dataForm.depositPrice = data.productDetails.depositPrice
                this.dataForm.salesPrice = data.productDetails.salesPrice
                this.dataForm.thumbnail = data.productDetails.thumbnail
                this.dataForm.stock = data.productDetails.stock
                this.dataForm.createTime = data.productDetails.createTime
                this.dataForm.modifyTime = data.productDetails.modifyTime
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
              url: this.$http.adornUrl(`/manager/productdetails/${!this.dataForm.productId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'productId': this.dataForm.productId || undefined,
                'productName': this.dataForm.productName,
                'packageId': this.dataForm.packageId,
                'saleType': this.dataForm.saleType,
                'depositPrice': this.dataForm.depositPrice,
                'salesPrice': this.dataForm.salesPrice,
                'thumbnail': this.dataForm.thumbnail,
                'stock': this.dataForm.stock,
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
      }
    }
  }
</script>
