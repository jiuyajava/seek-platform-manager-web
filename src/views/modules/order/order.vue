<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.orderNumber" placeholder="订单号" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <template>
          <el-select v-model="dataForm.orderStatus" placeholder="请选择订单状态">
            <el-option
              v-for="item in statusOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value" >
            </el-option>
          </el-select>
        </template>
      </el-form-item>
      <el-form-item>
        <template>
          <el-select v-model="dataForm.payment" placeholder="请选择支付方式">
            <el-option
              v-for="item in paymentOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value" >
            </el-option>
          </el-select>
        </template>
      </el-form-item>
      <el-form-item>
        <template>
          <el-select v-model="dataForm.productType" placeholder="请选择商品方式">
            <el-option
              v-for="item in productTypeOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value" >
            </el-option>
          </el-select>
        </template>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      style="width: 100%;">
      <!-- <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column> -->
      <el-table-column
        prop="orderId"
        header-align="center"
        align="center"
        label="主键ID">
      </el-table-column>
      <el-table-column
        prop="orderNumber"
        header-align="center"
        align="center"
        width="160"
        label="订单编号">
        <template slot-scope="scope">
          <el-popover trigger="hover" placement="top">
            <p>下单时间: {{ scope.row.createTime }}</p>
            <p>支付时间: {{ scope.row.payTime }}</p>
            <div slot="reference" >
              <el-tag size="medium" type="info">{{ scope.row.orderNumber }}</el-tag>
            </div>
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column prop="orderStatus"
        header-align="center"
        align="center"
        label="订单状态"
        width="95">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.orderStatus === 1" size="small" type="danger">待支付</el-tag>
          <el-tag v-else-if="scope.row.orderStatus == 2" size="small" type="success">待出行</el-tag>
          <el-tag v-else-if="scope.row.orderStatus == 3" size="small" type="success">已出行</el-tag>
          <el-tag v-else-if="scope.row.orderStatus == 4" size="small" type="danger">取消订单</el-tag>
          <el-tag v-else-if="scope.row.orderStatus == 5" size="small" type="danger">退货/退款</el-tag>
          <el-tag v-else-if="scope.row.orderStatus == 6" size="small" type="success">退款成功</el-tag>
          <el-tag v-else-if="scope.row.orderStatus == 7" size="small" type="success">退款失败</el-tag>
          <el-tag v-else-if="scope.row.orderStatus == 8" size="small" type="success">已完成</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="payment"
        header-align="center"
        align="center"
        label="支付方式"
        width="90">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.payment === 1" size="small" type="success">微信</el-tag>
          <el-tag v-else-if="scope.row.payment == 2" size="small" type="danger">支付宝</el-tag>
        </template>
      </el-table-column>
      <!-- <el-table-column
        prop="productId"
        header-align="center"
        align="center"
        label="商品ID">
      </el-table-column> -->
      <el-table-column
        prop="productName"
        header-align="center"
        align="center"
        width="170"
        label="商品名称">
        <template slot-scope="scope">
          <el-popover trigger="hover" placement="top">
            <p>商品ID: {{ scope.row.productId }}</p>
            <p>商品单价: {{ scope.row.productPrice }}  元</p>
            <div slot="reference" >
              <el-tag size="medium" type="">{{ scope.row.productName }}</el-tag>
            </div>
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column
        prop="productThumbnail"
        header-align="center"
        align="center"
        label="商品图片">
        <template slot-scope="scope">
          <img min-width="60" height="60" :src="scope.row.productThumbnail" />
        </template>
      </el-table-column>
      <el-table-column
        prop="count"
        header-align="center"
        align="center"
        label="购买数量">
      </el-table-column>
      <el-table-column prop="productType"
        header-align="center"
        align="center"
        label="订单类型"
        width="90">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.productType === 1" size="small" type="success">全款订单</el-tag>
          <el-tag v-else-if="scope.row.productType == 2" size="small" type="danger">定金订单</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="totalPrice"
        header-align="center"
        align="center"
        label="订单金额">
      </el-table-column>
      <el-table-column
        prop="travelDate"
        header-align="center"
        align="center"
        label="出行时间">
      </el-table-column>
      <el-table-column
        prop="contact"
        header-align="center"
        align="center"
        label="联系人">
      </el-table-column>
      <el-table-column
        prop="mobile"
        header-align="center"
        align="center"
        label="联系电话">
      </el-table-column>
      <el-table-column
        prop="sellerName"
        header-align="center"
        align="center"
        label="商户名称">
      </el-table-column>
      <el-table-column
        prop="sellerMobile"
        header-align="center"
        align="center"
        label="商户电话">
      </el-table-column>
      <el-table-column
        prop="checkCode"
        header-align="center"
        align="center"
        label="核销码">
      </el-table-column>

      <!-- 
        <el-table-column
        prop="productPrice"
        header-align="center"
        align="center"
        label="单价">
      </el-table-column>
        <el-table-column
        prop="payTime"
        header-align="center"
        align="center"
        label="支付时间">
      </el-table-column>
        <el-table-column
        prop="message"
        header-align="center"
        align="center"
        label="订单留言">
      </el-table-column> -->
      <!-- <el-table-column
        prop="remark"
        header-align="center"
        align="center"
        label="备注信息">
      </el-table-column>
        <el-table-column
        prop="playAddress"
        header-align="center"
        align="center"
        label="游玩地址">
        </el-table-column> -->
        <!-- <el-table-column
          prop="buyerId"
          header-align="center"
          align="center"
          label="用户ID">
        </el-table-column> -->
        <!-- <el-table-column
          prop="sellerId"
          header-align="center"
          align="center"
          label="商户ID">
        </el-table-column>
        <el-table-column
        prop="longitudeLatitude"
        header-align="center"
        align="center"
        label="经纬度">
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        label="下单时间">
      </el-table-column> -->
      <!-- <el-table-column
        prop="modifyTime"
        header-align="center"
        align="center"
        label="更新时间">
      </el-table-column> -->
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="showDetails(scope.row.orderId)">查看</el-button>
          <el-button type="text" size="small" @click="operationOrder(scope.row.orderId)">订单处理</el-button>
          <el-button type="text" size="small" @click="sendMsg(scope.row.orderId, scope.row.orderNumber, scope.row.orderStatus, scope.row.contact, scope.row.mobile, scope.row.createTime, scope.row.travelDate)">用户短信反馈</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 查看详情 -->
    <order-details v-if="orderDetailsVisible" ref="orderDetails" @refreshDataList="getDataList"></order-details>
    <order-update v-if="orderUpdateVisible" ref="orderUpdate" @refreshDataList="getDataList"></order-update>
    <order-message v-if="sendMsgVisible" ref="orderMessage" @refreshDataList="getDataList"></order-message>
  </div>
</template>

<script>
  import OrderDetails from './order-details'
  import OrderUpdate from './order-update'
  import OrderMessage from './order-message'
  export default {
    data () {
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
        productTypeOptions: [{
          value: 0,
          label: '全部订单类型'
        }, {
          value: 1,
          label: '全款订单'
        }, {
          value: 2,
          label: '定金订单'
        }],
        paymentOptions: [{
          value: 0,
          label: '全部支付方式'
        }, {
          value: 1,
          label: '微信'
        }, {
          value: 2,
          label: '支付宝'
        }],
        dataForm: {
          orderNumber: '',
          payment: 0,
          orderStatus: 0,
          productType: 0
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        orderDetailsVisible: false,
        orderUpdateVisible: false,
        sendMsgVisible: false
      }
    },
    components: {
      OrderDetails,
      OrderUpdate,
      OrderMessage
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/manager/order/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'orderNumber': this.dataForm.orderNumber,
            'payment': this.dataForm.payment,
            'orderStatus': this.dataForm.orderStatus,
            'productType': this.dataForm.productType
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 查看详情
      showDetails (id) {
        this.orderDetailsVisible = true
        this.orderUpdateVisible = false
        this.$nextTick(() => {
          this.$refs.orderDetails.init(id)
        })
      },
      // 订单处理
      operationOrder (id) {
        this.orderUpdateVisible = true
        this.orderDetailsVisible = false
        this.$nextTick(() => {
          this.$refs.orderUpdate.init(id)
        })
      },
      // 用户反馈
      sendMsg (orderId, orderNumber, orderStatus, contact, mobile, createTime, travelDate) {
        this.sendMsgVisible = true
        this.$nextTick(() => {
          this.$refs.orderMessage.init(orderId, orderNumber, orderStatus, contact, mobile, createTime, travelDate)
        })
      }
    }
  }
</script>
