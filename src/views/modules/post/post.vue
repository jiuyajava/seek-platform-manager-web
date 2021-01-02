<template>
  <div class="mod-post">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
     <el-form-item>
        <el-input v-model="dataForm.nickname" placeholder="用户昵称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.title" placeholder="标题" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <template>
          <el-select v-model="dataForm.checkState" placeholder="审核状态" clearable>
            <el-option
              v-for="item in checkStateOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
        </template>
      </el-form-item>
      <el-form-item>
        <template>
          <el-select v-model="dataForm.state" placeholder="状态" clearable>
            <el-option
              v-for="item in stateOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value">
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
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
     <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
     <el-table-column
        prop="id"
        header-align="center"
        align="center"
        width="80"
        label="ID">
      </el-table-column>
     <el-table-column
        prop="uid"
        header-align="center"
        align="center"
        width="80"
        label="用户ID">
      </el-table-column>
     <el-table-column
        prop="nickname"
        header-align="center"
        align="center"
        label="用户名">
      </el-table-column>
     <!-- <el-table-column
        prop="section"
        header-align="center"
        align="center"
        label="版块">
         <template slot-scope="scope">
          <el-tag v-if="scope.row.section === 1" size="small" type="info">玩天空</el-tag>
          <el-tag v-else-if="scope.row.section === 2" size="small" type="info">极鸥游</el-tag>
        </template>
      </el-table-column> -->
      <el-table-column
        prop="title"
        header-align="center"
        align="center"
        label="标题">
      </el-table-column>
      <el-table-column
        prop="type"
        header-align="center"
        align="center"
        width="80"
        label="类型">
         <template slot-scope="scope">
          <el-tag v-if="scope.row.type === 1" size="small" type="info">图片</el-tag>
          <el-tag v-else-if="scope.row.type === 2" size="small" type="info">视频</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="image"
        header-align="center"
        align="center"
        label="资源">
        <template slot-scope="scope">
          <img min-width="60"  v-if="scope.row.type === 1"  height="60" :src="scope.row.imgUrl[0]" />
          <img min-width="60"  v-if="scope.row.type === 2"  height="60" :src="scope.row.vframe" />
        </template>
      </el-table-column>
      <el-table-column
        prop="checkState"
        header-align="center"
        align="center"
        width="80"
        label="审核状态">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.checkState === 0" size="small" type="info">未审核</el-tag>
          <el-tag v-else-if="scope.row.checkState === 1" size="small" type="info">已审核</el-tag>
          <el-tag v-else-if="scope.row.checkState === 2" size="small" type="info">审核中</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="state"
        header-align="center"
        align="center"
        width="80"
        label="审核结果">
         <template slot-scope="scope">
          <el-tag v-if="scope.row.state === -1" size="small" type="info">默认状态</el-tag>
          <el-tag v-else-if="scope.row.state === 0" size="small" type="info">正常</el-tag>
          <el-tag v-else-if="scope.row.state === 1" size="small" type="info">涉黄</el-tag>
          <el-tag v-else-if="scope.row.state === 2" size="small" type="info">涉恐</el-tag>
          <el-tag v-else-if="scope.row.state === 3" size="small" type="info">涉政</el-tag>
          <el-tag v-else-if="scope.row.state === 4" size="small" type="info">广告</el-tag>
          <el-tag v-else-if="scope.row.state === 5" size="small" type="info">其他</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="isTop"
        header-align="center"
        align="center"
        width="80"
        label="置顶">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.isTop === 0" size="small" type="info">否</el-tag>
          <el-tag v-else-if="scope.row.isTop === 1" size="small" type="info">是</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="isDelete"
        header-align="center"
        align="center"
         width="80"
        label="资源状态">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.isDelete === 0" size="small" type="info">正常</el-tag>
          <el-tag v-else-if="scope.row.isDelete === 1" size="small" type="info">已删除</el-tag>
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
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row)">详情</el-button>
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
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" ></add-or-update>
  </div>
</template>
<script>
  import AddOrUpdate from './detail-post'
  export default {
    data () {
      return {
        checkStateOptions: [{
          value: '0',
          label: '未审核'
        }, {
          value: '1',
          label: '已审核'
        }, {
          value: '2',
          label: '审核中'
        }],
        stateOptions: [{
          value: '-1',
          label: '默认状态'
        }, {
          value: '0',
          label: '正常'
        }, {
          value: '1',
          label: '涉黄'
        }, {
          value: '2',
          label: '涉恐'
        }, {
          value: '3',
          label: '涉政'
        }, {
          value: '4',
          label: '广告'
        }, {
          value: '5',
          label: '其他'
        }],
        dataForm: {
          nickname: '',
          title: '',
          checkState: '',
          state: ''
        },
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
          url: this.$http.adornUrl('/manager/post/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'nickname': this.dataForm.nickname,
            'title': this.dataForm.title,
            'checkState': this.dataForm.checkState,
            'state': this.dataForm.state
            // 'roleType': this.dataForm.roleType
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
      // 详情页
      addOrUpdateHandle (row) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(row)
        })
      }
    }
  }
</script>
