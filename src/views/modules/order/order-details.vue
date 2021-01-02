<template>
  <el-dialog
    title="订单详情"
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
        <el-form-item label="核销码" prop="checkCode">
          <el-input v-model="dataForm.checkCode" disabled placeholder="核销码" style="width: 80%"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
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
            <el-radio :label="2">订金商品</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col>
    </el-row>
    <el-form-item label="订单状态" prop="orderStatus">
      <el-radio-group v-model="dataForm.orderStatus" disabled>
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
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="出行日期" prop="travelDate">
          <el-date-picker type="date" disabled placeholder="出行日期" v-model="dataForm.travelDate" style="width: 80%;"></el-date-picker>
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
      <el-input type="textarea" disabled v-model="dataForm.remark" placeholder="平台备注" :autosize="{ minRows: 3, maxRows: 8}" style="width: 92%"></el-input>
    </el-form-item>
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
    <el-form-item label="游玩地址" prop="playAddress">
      <el-input v-model="dataForm.playAddress" disabled placeholder="游玩地址" style="width: 92%" ></el-input>
    </el-form-item>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="买家ID" prop="buyerId">
          <el-input v-model="dataForm.buyerId" disabled placeholder="买家ID" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="买家昵称" prop="payTime">
          <el-input v-model="dataForm.nickname" disabled placeholder="买家昵称" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <el-row type="flex">
      <el-col :span="11">
        <el-form-item label="商户名称" prop="count">
          <el-input v-model="dataForm.sellerName" disabled placeholder="商户名称" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-form-item label="商户电话" prop="payTime">
          <el-input v-model="dataForm.sellerMobile" disabled placeholder="商户电话" style="width: 80%;"></el-input>
        </el-form-item>
      </el-col>
    </el-row>
    <template v-if="dataForm.orderId">
      <el-row type="flex">
        <el-col :span="11">
          <el-form-item label="下单时间" prop="createTime">
            <el-date-picker v-model="dataForm.createTime" type="datetime" disabled placeholder="下单时间" style="width: 80%;" readonly></el-date-picker>
          </el-form-item>
        </el-col>
        <el-col class="line" :span="2">-</el-col>
        <el-col :span="11">
          <el-form-item label="编辑时间" prop="modifyTime">
            <el-date-picker v-model="dataForm.modifyTime" type="datetime" disabled placeholder="编辑时间" style="width: 80%;" readonly></el-date-picker>
          </el-form-item>
        </el-col>
      </el-row>
    </template>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">返回  </el-button>
      <el-button type="primary" @click="visible = false">确定</el-button>
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
          payment: '',
          productType: '',
          productId: '',
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
          playAddress: '',
          buyerId: '',
          nickname: '',
          sellerId: '',
          sellerName: '',
          sellerMobile: '',
          longitudeLatitude: '',
          checkCode: '',
          cancelTime: '',
          serviceTime: '',
          serviceDesc: '',
          createTime: '',
          modifyTime: ''
        },
        dataRule: {
          // orderStatus: [
          //   { required: true, message: '订单状态不能为空', trigger: 'blur' }
          // ],
          // travelDate: [
          //    { type: 'date', required: true, message: '出行时间不能为空', trigger: 'change' }
          // ]
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
              url: this.$http.adornUrl(`/manager/order/info/${this.dataForm.orderId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.orderNumber = data.order.orderNumber
                this.dataForm.orderStatus = data.order.orderStatus
                this.dataForm.payment = data.order.payment
                this.dataForm.productType = data.order.productType
                this.dataForm.productId = data.order.productId
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
                this.dataForm.playAddress = data.order.playAddress
                this.dataForm.buyerId = data.order.buyerId
                this.dataForm.nickname = data.order.nickname
                this.dataForm.sellerId = data.order.sellerId
                this.dataForm.sellerName = data.order.sellerName
                this.dataForm.sellerMobile = data.order.sellerMobile
                this.dataForm.longitudeLatitude = data.order.longitudeLatitude
                this.dataForm.checkCode = data.order.checkCode
                this.dataForm.cancelTime = data.order.cancelTime
                this.dataForm.serviceTime = data.order.serviceTime
                this.dataForm.serviceDesc = data.order.serviceDesc
                this.dataForm.createTime = data.order.createTime
                this.dataForm.modifyTime = data.order.modifyTime
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
                'payment': this.dataForm.payment,
                'productType': this.dataForm.productType,
                'productId': this.dataForm.productId,
                'productName': this.dataForm.productName,
                'productThumbnail': this.dataForm.productThumbnail,
                'count': this.dataForm.count,
                'productPrice': this.dataForm.productPrice,
                'totalPrice': this.dataForm.totalPrice,
                'payTime': this.dataForm.payTime,
                'contact': this.dataForm.contact,
                'mobile': this.dataForm.mobile,
                'message': this.dataForm.message,
                'remark': this.dataForm.remark,
                'travelDate': this.dataForm.travelDate,
                'playAddress': this.dataForm.playAddress,
                'buyerId': this.dataForm.buyerId,
                'sellerId': this.dataForm.sellerId,
                'sellerName': this.dataForm.sellerName,
                'sellerMobile': this.dataForm.sellerMobile,
                'longitudeLatitude': this.dataForm.longitudeLatitude,
                'checkCode': this.dataForm.checkCode,
                'cancelTime': this.dataForm.cancelTime,
                'serviceTime': this.dataForm.serviceTime,
                'serviceDesc': this.dataForm.serviceDesc,
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
