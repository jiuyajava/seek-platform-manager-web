<template>
  <div class="mod-user">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.nickname" placeholder="用户昵称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.mobile" placeholder="手机号" clearable></el-input>
      </el-form-item>
      <!-- <el-form-item>
        <template>
          <el-select v-model="dataForm.roleType" placeholder="请选择用户身份">
            <el-option
              v-for="item in roleOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
        </template>
      </el-form-item> -->
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('sys:user:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('manager:user:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量禁用</el-button>
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
        prop="userId"
        header-align="center"
        align="center"
        width="80"
        label="ID">
      </el-table-column>
      <el-table-column
        prop="nickname"
        header-align="center"
        align="center"
        label="昵称">
        <template slot-scope="scope">
            <el-popover trigger="hover" placement="top">
            <p>用户名: {{ scope.row.username }}</p>
            <p>三方标识: {{ scope.row.thirdPartyId }}</p>
            <div slot="reference" >
                <el-tag size="medium"  type="info">{{ scope.row.nickname }}</el-tag>
            </div>
            </el-popover>
        </template>
      </el-table-column>
      <el-table-column
        prop="avatar"
        header-align="center"
        align="center"
        label="用户图像">
        <template slot-scope="scope">
            <img min-width="60" height="60" :src="scope.row.avatar" />
        </template>
      </el-table-column>
      <el-table-column
        prop="mobile"
        header-align="center"
        align="center"
        label="手机号">
      </el-table-column>
      <!-- <el-table-column
        prop="roleType"
        header-align="center"
        align="center"
        label="用户身份">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.roleType === 1" size="small">普通用户</el-tag>
          <el-tag v-else size="small" type="success">上游商户</el-tag>
        </template>
      </el-table-column> -->
      <el-table-column
        prop="gender"
        header-align="center"
        align="center"
        label="性别">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.gender === 1" size="small" type="info">男</el-tag>
          <el-tag v-else-if="scope.row.gender === 2" size="small" type="info">女</el-tag>
          <el-tag v-else size="small" type="danger">未知</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="账号状态">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.status === 1" size="small" type="success">正常</el-tag>
          <el-tag v-else size="small" type="danger">禁用</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="source"
        header-align="center"
        align="center"
        label="注册来源">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.source === 1" size="small" type="success">手机号 + 验证码</el-tag>
          <el-tag v-else-if="scope.row.source === 2" size="small" type="success">微信</el-tag>
          <el-tag v-else-if="scope.row.source === 3" size="small" type="success">AppleId</el-tag>
          <el-tag v-else size="small">手机号 + 密码</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="birthday"
        header-align="center"
        align="center"
        width="180"
        label="生日">
      </el-table-column>  
      <el-table-column
        prop="city"
        header-align="center"
        align="center"
        width="180"
        label="所在城市">
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        width="180"
        label="注册时间">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button v-if="isAuth('sys:user:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.userId)">修改</el-button>
          <el-button   type="text" size="small" @click="blackListHandle(scope.row.userId)">拉黑</el-button>
          <el-button v-if="isAuth('manager:user:delete') && scope.row.status === 1" type="text" size="small" @click="deleteHandle(scope.row.userId)">禁用</el-button>
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
  import AddOrUpdate from './user-add-or-update'
  export default {
    data () {
      return {
        roleOptions: [{
          value: '1',
          label: '普通用户'
        }, {
          value: '2',
          label: '星主'
        }],

        dataForm: {
          nickname: '',
          mobile: ''
          // roleType: ''
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
          url: this.$http.adornUrl('/manager/user/users'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'nickname': this.dataForm.nickname,
            'mobile': this.dataForm.mobile
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
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      blackListHandle (id) {
        this.$http({
          url: this.$http.adornUrl('/manager/blackList/save'),
          method: 'post',
          data: this.$http.adornData({
            'type': 1,
            'userId': id
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '操作成功',
              type: 'success',
              duration: 1500
            })
          } else {
            this.$message.error(data.msg)
          }
        })
      },
      // 删除
      deleteHandle (id) {
        let flag = 0
        var userIds = id ? [id] : this.dataListSelections.map(item => {
          if (item.status === 0) {
            flag = flag + 1
          } else {
            return item.userId
          }
        })

        if (flag > 0) {
          this.$message.error(`批量操作条目中，含有【${flag}】个已禁用用户，请重新选择！`)
          return
        }

        this.$confirm(`确定对[id=${userIds.join(',')}]进行[${id ? '账号禁用' : '批量禁用'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/manager/user/delete'),
            method: 'post',
            data: this.$http.adornData(userIds, false)
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
        }).catch(() => {})
      }
    }
  }
</script>
