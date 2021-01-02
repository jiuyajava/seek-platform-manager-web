<template>
  <el-dialog
    :title="'详情'"
    :close-on-click-modal="false"
    :visible.sync="visible"
  >
    <el-form :model="dataForm"  ref="dataForm"  :rules="dataRule" label-width="80px">
      <el-row>
        <el-col :span="8"
          ><div class="grid-content bg-purple">
            <el-form-item label="帖子ID" prop="name">
              <el-input
                v-model="dataForm.id"
                placeholder="名称"
                :disabled="true"
              ></el-input>
            </el-form-item></div
        ></el-col>
        <el-col :span="8"
          ><div class="grid-content bg-purple-light">
            <el-form-item label="用户ID" prop="code">
              <el-input v-model="dataForm.uid" :disabled="true"></el-input>
            </el-form-item></div
        ></el-col>
        <el-col :span="8"
          ><div class="grid-content bg-purple">
            <el-form-item label="用户名" prop="code">
              <el-input v-model="dataForm.nickname" :disabled="true"></el-input>
            </el-form-item></div
        ></el-col>
      </el-row>
      <el-row>
        <el-col :span="8"
          ><div class="grid-content bg-purple-light">
            <el-form-item label="版块">
              <el-select v-model="dataForm.section" :disabled="true">
                <el-option
                  v-for="item in sectionOptions"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                >
                </el-option>
              </el-select>
            </el-form-item></div
        ></el-col>
        <el-col :span="8"
          ><div class="grid-content bg-purple-light">
            <el-form-item label="标签">
              <el-input v-model="dataForm.postType" :disabled="true"></el-input>
            </el-form-item></div
        ></el-col>
      </el-row>
      <el-row>
        <el-col :span="12"
          ><div class="grid-content bg-purple">
            <el-form-item label="标题">
              <el-input v-model="dataForm.title" :disabled="true"></el-input>
            </el-form-item></div
        ></el-col>
      </el-row>
      <el-row type="flex" v-if="dataForm.type == 1">
        <el-col>
          <el-form-item label="图片">
            <viewer
              :images="dataForm.imgUrl"
              @inited="inited"
              ref="viewer"
              :index="0"
            >
              <template>
                <img
                  style="width: 90px; height: auto; vertical-align: middle"
                  v-for="(src, index) in dataForm.imgUrl"
                  :src="src"
                  :key="index"
                />
              </template>
            </viewer>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row type="flex" v-if="dataForm.type == 2">
        <el-col>
          <el-form-item label="视频">
            <template>
              <div>
                <video-player :options="videoOptions" />
              </div>
            </template>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row type="flex">
        <el-col>
          <el-form-item label="内容">
            <el-input type="textarea" :rows="2" v-model="dataForm.content">
            </el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="8"
          ><div class="grid-content bg-purple"></div>
          <el-form-item label="点赞数量">
            <el-input v-model="dataForm.likeCounts" :disabled="true">
            </el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8"
          ><div class="grid-content bg-purple-light">
            <el-form-item label="评论数量">
              <el-input v-model="dataForm.commentCounts" :disabled="true">
              </el-input>
            </el-form-item></div
        ></el-col>
        <el-col :span="8"
          ><div class="grid-content bg-purple">
            <el-form-item label="审核状态">
              <el-select v-model="dataForm.checkState" :disabled="true">
                <el-option
                  v-for="item in checkStateOptions"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                >
                </el-option>
              </el-select>
            </el-form-item></div
        ></el-col>
      </el-row>
      <el-row>
        <el-col :span="8"
          ><div class="grid-content bg-purple">
            <el-form-item label="置顶">
              <el-radio-group v-model="dataForm.isTop">
                <el-radio :label="0">否</el-radio>
                <el-radio :label="1">是</el-radio>
              </el-radio-group>
            </el-form-item>
          </div></el-col
        >
        <el-col :span="8"
          ><div class="grid-content bg-purple-light">
            <el-form-item label="审核人">
              <el-input v-model="dataForm.reviewer" :disabled="true">
              </el-input>
            </el-form-item></div
        ></el-col>
        <el-col :span="8"
          ><div class="grid-content bg-purple">
            <el-form-item label="审核时间" prop="createTime">
              <el-date-picker
                v-model="dataForm.reviewerTime"
                type="datetime"
                placeholder="审核时间"
                :disabled="true"
              ></el-date-picker>
            </el-form-item></div
        ></el-col>
      </el-row>
      <el-row>
        <el-col :span="8"
          ><div class="grid-content bg-purple">
            <el-form-item label="删除">
              <el-radio-group v-model="dataForm.isDelete" :disabled="true">
                <el-radio :label="0">否</el-radio>
                <el-radio :label="1">是</el-radio>
              </el-radio-group>
            </el-form-item>
          </div></el-col
        >
        <el-col :span="8"
          ><div class="grid-content bg-purple-light">
            <el-form-item label="位置">
              <el-input v-model="dataForm.location" :disabled="true">
              </el-input>
            </el-form-item></div
        ></el-col>
        <el-col :span="8"
          ><div class="grid-content bg-purple">
            <el-form-item label="创建时间" prop="createTime">
              <el-date-picker
                v-model="dataForm.createTime"
                type="datetime"
                placeholder="创建时间"
                :disabled="true"
              ></el-date-picker>
            </el-form-item></div
        ></el-col>
      </el-row>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
import VideoPlayer from './VideoPlayer'
export default {
  components: {
    VideoPlayer
  },
  data () {
    return {
      dataRule: {},
      sectionOptions: [
        {
          value: 1,
          label: '玩天空'
        },
        {
          value: 2,
          label: '极鸥游'
        }
      ],
      checkStateOptions: [
        {
          value: 0,
          label: '未审核'
        },
        {
          value: 1,
          label: '已审核'
        }
      ],
      visible: false,
      videoOptions: {
        autoplay: 'muted', // 自动播放
        controls: true, // 用户可以与之交互的控件
        loop: false, // 视频一结束就重新开始
        muted: false, // 默认情况下将使所有音频静音
        aspectRatio: '16:9', // 显示比率
        fullscreen: {
          options: { navigationUI: 'hide' }
        },
        sources: [
          {
            src: '',
            type: 'video/mp4'
          }
        ]
      },
      dataForm: {
        nickname: ''
      }
    }
  },
  methods: {
    init (row) {
      this.visible = true
      this.dataForm = row
      this.videoOptions.sources[0].src = row.videoUrl
    },
    inited (viewer) {
      this.$viewer = viewer
      this.$viewer.index = this.activeIndex
      // 不要他的按钮
      this.$viewer.options.button = false
      // 不要他的底部缩略图
      this.$viewer.options.navbar = false
      // 不要他的底部标题
      this.$viewer.options.title = false
      // 不要他的底部工具栏
      this.$viewer.options.toolbar = false
      // this.show ()
    },
    // 调用显示
    show () {
      this.$viewer.show()
    },
          // 表单提交
    dataFormSubmit () {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
        }
        this.$http({
          url: this.$http.adornUrl(`/manager/post/${!this.dataForm.id ? 'save' : 'update'}`),
          method: 'post',
          data: this.$http.adornData({
            'id': this.dataForm.id || undefined,
            'isTop': this.dataForm.isTop
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
      })
    }
  }
}
</script>