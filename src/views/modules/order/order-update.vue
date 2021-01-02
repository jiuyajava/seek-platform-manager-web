<template>
  <el-dialog
    title="订单处理"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="订单编号" prop="productName">
          <el-input v-model="dataForm.orderNumber" disabled placeholder="订单编号" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="售卖类型" prop="productType">
          <el-radio-group v-model="dataForm.productType" disabled>
            <el-radio :label="1">全款订单</el-radio>
            <el-radio :label="2">定金商品</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col>
    </el-row>
    <!-- 
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="支付方式" prop="payment">
          <el-radio-group v-model="dataForm.payment" disabled>
            <el-radio :label="1">微信</el-radio>
            <el-radio :label="2">支付宝</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="售卖类型" prop="productType">
          <el-radio-group v-model="dataForm.productType" disabled>
            <el-radio :label="1">全款订单</el-radio>
            <el-radio :label="2">定金商品</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col>
    </el-row>
    -->
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="商品名称" prop="productName">
          <el-input v-model="dataForm.productName" disabled placeholder="商品名称" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="商品单价" prop="productPrice">
          <el-input v-model="dataForm.productPrice" disabled placeholder="商品单价" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="购买数量" prop="count">
          <el-input v-model="dataForm.count" disabled placeholder="购买数量" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="订单金额" prop="totalPrice">
          <el-input v-model="dataForm.totalPrice" disabled placeholder="订单金额" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
    </el-row>

    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="联系人" prop="count">
          <el-input v-model="dataForm.contact" disabled placeholder="联系人" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="联系电话" prop="payTime">
          <el-input v-model="dataForm.mobile" disabled placeholder="联系电话" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-row>
      <el-col :span="11">
        <el-form-item label="商品图片" prop="productThumbnail">
            <img min-width="100" height="100" :src="dataForm.productThumbnail" />
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="订单留言" prop="message">
          <el-input type="textarea" v-model="dataForm.message" disabled placeholder="订单留言" :autosize="{ minRows: 4, maxRows: 8}" style="width: 80%"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-form-item label="订单状态" prop="orderStatus">
      <el-radio-group v-model="dataForm.orderStatus">
        <el-radio :label="1">待支付</el-radio>
        <el-radio :label="2">待出行</el-radio>
        <el-radio :label="3">已出行</el-radio>
        <el-radio :label="4">取消订单</el-radio>
        <el-radio :label="5">退货/退款</el-radio>
        <el-radio :label="6">退款成功</el-radio>
        <el-radio :label="7">退款失败</el-radio>
        <el-radio :label="8">已完成</el-radio>
      </el-radio-group>
    </el-form-item>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="出行时间" prop="travelDate">
          <el-date-picker type="date" placeholder="出行时间" value-format="yyyy-MM-dd" v-model="dataForm.travelDate" style="width: 80%;"></el-date-picker>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="支付时间" prop="payTime">
          <el-input v-model="dataForm.payTime" disabled placeholder="支付时间" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-form-item label="平台备注" prop="remark">
      <el-input type="textarea" v-model="dataForm.remark" placeholder="平台备注" :autosize="{ minRows: 3, maxRows: 8}" style="width: 92%"></el-input>
    </el-form-item>

    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">返回  </el-button>
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
          orderId: 0,
          orderNumber: '',
          orderStatus: '',
          productType: '',
          productName: '',
          productThumbnail: '',
          count: '',
          productPrice: '',
          totalPrice: '',
          payTime: '',
          contact: '',
          mobile: '',
          message: '',
          remark: '',
          travelDate: '',
          createTime: ''
        },
        dataRule: {
          orderStatus: [
            { required: true, message: '订单状态不能为空', trigger: 'blur' }
          ],
          travelDate: [
             { type: 'string', required: true, message: '出行时间不能为空', trigger: 'change' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.orderId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.orderId) {
            this.$http({
              url: this.$http.adornUrl(`/manager/order/operation/${this.dataForm.orderId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.orderNumber = data.order.orderNumber
                this.dataForm.orderStatus = data.order.orderStatus
                this.dataForm.productType = data.order.productType
                this.dataForm.productName = data.order.productName
                this.dataForm.productThumbnail = data.order.productThumbnail
                this.dataForm.count = data.order.count
                this.dataForm.productPrice = data.order.productPrice
                this.dataForm.totalPrice = data.order.totalPrice
                this.dataForm.payTime = data.order.payTime
                this.dataForm.contact = data.order.contact
                this.dataForm.mobile = data.order.mobile
                this.dataForm.message = data.order.message
                this.dataForm.remark = data.order.remark
                this.dataForm.travelDate = data.order.travelDate
                this.dataForm.createTime = data.order.createTime
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
              url: this.$http.adornUrl(`/manager/order/${!this.dataForm.orderId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'orderId': this.dataForm.orderId || undefined,
                'orderNumber': this.dataForm.orderNumber,
                'orderStatus': this.dataForm.orderStatus,
                'remark': this.dataForm.remark,
                'travelDate': this.dataForm.travelDate
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
