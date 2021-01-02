<template>
  <div class="mod-post">
    <el-form>
        <el-form-item>
            <el-button  type="primary" @click="addOrUpdateHandle()">新增</el-button>
        </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      style="width: 100%;">
     <el-table-column
        prop="id"
        header-align="center"
        align="center"
        width="80"
        label="ID">
      </el-table-column>
     <el-table-column
        prop="client"
        header-align="center"
        align="center"
        label="客户端">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.client === 2" size="small" type="info">iOS</el-tag>
          <el-tag v-else-if="scope.row.client === 1" size="small" type="info">Andorid</el-tag>
        </template>
      </el-table-column>
     <el-table-column
        prop="type"
        header-align="center"
        align="center"
        
        label="Andorid平台类型">
      </el-table-column>
     <el-table-column
        prop="typeStr"
        header-align="center"
        align="center"
      
        label="Andorid平台">
      </el-table-column>
    <el-table-column
        prop="status"
        header-align="center"
        align="center"
      
        label="状态">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.status === 0" size="small" type="info">禁用</el-tag>
          <el-tag v-else-if="scope.row.status === 1" size="small" type="info">启用</el-tag>
        </template>
      </el-table-column>
     <el-table-column
        prop="edition"
        header-align="center"
        align="center"
        label="版本">
      </el-table-column>
      
      <el-table-column
          prop="content"
          header-align="center"
          align="center"
          label="描述">
        </el-table-column>
      <el-table-column
         header-align="center"
        align="center"
      label="链接">
      <template slot-scope="scope" v-if="scope.row.link">
        <el-popover trigger="hover" placement="top">
          <p>{{ scope.row.link }}</p>
          <div slot="reference" class="name-wrapper">
            <el-tag size="medium">链接</el-tag>
          </div>
        </el-popover>
      </template>
    </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        label="创建时间">
      </el-table-column>
       <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
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
  import AddOrUpdate from './edition-add-or-update'
  export default {
    data () {
      return {
        dataListSelections: [],
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
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
          url: this.$http.adornUrl('/manager/edition/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'size': this.pageSize
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.records
            this.totalPage = data.page.total
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
      // 详情页
      addOrUpdateHandle (row) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(row)
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
            url: this.$http.adornUrl('/manager/edition/delete'),
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
