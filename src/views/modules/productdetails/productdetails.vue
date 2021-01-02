<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.productName" placeholder="商品名称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.packageId" placeholder="套餐ID" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <template>
          <el-select v-model="dataForm.saleType" placeholder="请选择售卖类型">
            <el-option
              v-for="item in saleTypeOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value" >
            </el-option>
          </el-select>
        </template>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('manager:productdetails:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('manager:productdetails:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column
        prop="productId"
        header-align="center"
        align="center"
        label="商品ID">
      </el-table-column>
      <el-table-column
        prop="productName"
        header-align="center"
        align="center"
        label="商品名称">
      </el-table-column>
      <el-table-column
        prop="packageId"
        header-align="center"
        align="center"
        label="套餐ID">
      </el-table-column>
      <el-table-column prop="saleType"
        header-align="center"
        align="center"
        label="售卖类型"
        width="90">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.saleType === 1" size="small" type="success">全款商品</el-tag>
          <el-tag v-else-if="scope.row.saleType == 2" size="small" type="danger">订金商品</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="salesPrice"
        header-align="center"
        align="center"
        label="销售价格">
      </el-table-column>
      <el-table-column
        prop="depositPrice"
        header-align="center"
        align="center"
        label="订金价格">
      </el-table-column>
      <el-table-column
        prop="thumbnail"
        header-align="center"
        align="center"
        label="缩略图">
        <template slot-scope="scope">
          <img min-width="60" height="60" :src="scope.row.thumbnail" />
        </template>
      </el-table-column>
      <el-table-column
        prop="stock"
        header-align="center"
        align="center"
        label="库存数量">
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        label="创建时间">
      </el-table-column>
      <el-table-column
        prop="modifyTime"
        header-align="center"
        align="center"
        label="修改时间">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.productId)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.productId)">删除</el-button>
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
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
  import AddOrUpdate from './productdetails-add-or-update'
  export default {
    data () {
      return {
        saleTypeOptions: [{
          value: 0,
          label: '全部售卖类型'
        }, {
          value: 1,
          label: '全款商品'
        }, {
          value: 2,
          label: '订金商品'
        }],
        dataForm: {
          productName: '',
          packageId: '',
          saleType: 0
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/manager/productdetails/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'productName': this.dataForm.productName,
            'packageId': this.dataForm.packageId === '' ? 0 : this.dataForm.packageId,
            'saleType': this.dataForm.saleType
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
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle (productId) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(productId)
        })
      },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/manager/productdetails/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      }
    }
  }
</script>
