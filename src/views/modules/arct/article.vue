<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.classify" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('arct:article:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('arct:article:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
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
        label="id">
      </el-table-column>
      <el-table-column
        prop="authorId"
        header-align="center"
        align="center"
        label="作者关联ID">
      </el-table-column>
      <el-table-column
        prop="browseCount"
        header-align="center"
        align="center"
        label="浏览量">
      </el-table-column>
      <el-table-column
        prop="classify"
        header-align="center"
        align="center"
        label="类型">
      </el-table-column>
      <el-table-column
        prop="content"
        show-overflow-tooltip
        header-align="center"
        align="center"
        label="文章内容">
      </el-table-column>
      <el-table-column
        prop="thumbsUpCount"
        header-align="center"
        align="center"
        label="点赞数量">
      </el-table-column>
      <el-table-column
        prop="title"
        show-overflow-tooltip
        header-align="center"
        align="center"
        label="标题">
      </el-table-column>
      <el-table-column
        prop="mode"
        header-align="center"
        align="center"
        label="布局">
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        label="发表时间">
      </el-table-column>
      <el-table-column
        prop="collectionCount"
        header-align="center"
        align="center"
        label="收藏数量">
      </el-table-column>
      <el-table-column
        prop="cover"
        show-overflow-tooltip
        header-align="center"
        align="center"
        label="封面图">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
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
  import AddOrUpdate from './article-add-or-update'
  export default {
    data () {
      return {
        dataForm: {
          classify: ''
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
        let params = {}
        if (JSON.parse(this.$cookie.get('role')).roleId === 1) {
          params = {
            'page': this.pageIndex,
            'limit': this.pageSize,
            'classify': this.dataForm.classify,
            'role': 1
          }
        } else {
          params = {
            'page': this.pageIndex,
            'limit': this.pageSize,
            'classify': this.dataForm.classify,
            'authorId': JSON.parse(this.$cookie.get('author')).id
          }
        }
        this.$http({
          url: this.$http.adornUrl('/arct/article/list'),
          method: 'get',
          params: this.$http.adornParams(params)
        }).then(({data}) => {
          console.log(JSON.parse(this.$cookie.get('role')))
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
        if (id === undefined) {
          console.log('111')
          this.$router.push({path: '/upload-article/add'})
        } else {
          this.$router.push({path: `/upload-article/${id}`})
        }
        // this.addOrUpdateVisible = true
        // this.$nextTick(() => {
        //   this.$refs.addOrUpdate.init(id)
        // })
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
            url: this.$http.adornUrl('/arct/article/delete'),
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
