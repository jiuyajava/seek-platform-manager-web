<template>
  <el-dialog
    title="用户短信反馈"
    :center="true"
    :close-on-click-modal="true"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <template v-if="dataForm.orderId">
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
      <el-row type="flex">
        <el-col :span="11">
          <el-form-item label="订单号" prop="orderNumber">
            <el-input v-model="dataForm.orderNumber" disabled placeholder="订单号" style="width: 80%;"></el-input>
          </el-form-item>
        </el-col>
        <el-col class="line" :span="2">-</el-col>
        <el-col :span="11">
          <el-form-item label="订单状态" prop="orderStatus">
            <template>
              <el-select v-model="dataForm.orderStatus" disabled placeholder="请选择订单状态" style="width: 80%;">
                <el-option
                  v-for="item in statusOptions"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value" >
                </el-option>
              </el-select>
            </template>
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
          <el-form-item label="下单时间" prop="createTime">
            <el-date-picker v-model="dataForm.createTime" type="datetime" disabled placeholder="创建时间" style="width: 80%;" readonly></el-date-picker>
          </el-form-item>
        </el-col>
      </el-row>
      <el-form-item label="通知类型" prop="messageType">
        <el-radio-group v-model="dataForm.messageType">
          <el-radio :label="5">订单双向确认成功</el-radio>
          <el-radio :label="6">订单双向确认失败</el-radio>
          <el-radio :label="7">订单出行日期修改成功</el-radio>
        </el-radio-group>
      </el-form-item>
    </template>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">返回  </el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import { isNumber } from '@/utils/validate'

  export default {
    data () {
      var validateMessageType = (rule, value, callback) => {
        if (!isNumber(value)) {
          callback(new Error('请选择通知业务类型'))
        } else {
          callback()
        }
      }
      return {
        statusOptions: [{
          value: 0,
          label: '全部订单状态'
        }, {
          value: 1,
          label: '待支付'
        }, {
          value: 2,
          label: '待出行'
        }, {
          value: 3,
          label: '已出行'
        }, {
          value: 4,
          label: '取消订单'
        }, {
          value: 5,
          label: '退货/退款'
        }, {
          value: 6,
          label: '退款成功'
        }, {
          value: 7,
          label: '退款失败'
        }, {
          value: 8,
          label: '已完成'
        }],
        visible: false,
        dataForm: {
          messageType: 0,
          contact: '',
          mobile: '',
          orderId: 0,
          orderStatus: 0,
          orderNumber: '',
          createTime: '',
          travelDate: ''
        },
        dataRule: {
          messageType: [
            { required: true, message: '请选择通知业务类型', trigger: 'blur' },
            { validator: validateMessageType, trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (orderId, orderNumber, orderStatus, contact, mobile, createTime, travelDate) {
        this.visible = true
        this.dataForm.messageType = 0
        this.dataForm.orderNumber = orderNumber
        this.dataForm.orderStatus = orderStatus
        this.dataForm.orderId = orderId
        this.dataForm.contact = contact
        this.dataForm.mobile = mobile
        this.dataForm.createTime = createTime
        this.dataForm.travelDate = travelDate
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl('/manager/order/message/'),
              method: 'post',
              data: this.$http.adornData({
                'orderId': this.dataForm.orderId,
                'orderNumber': this.dataForm.orderNumber,
                'messageType': this.dataForm.messageType
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