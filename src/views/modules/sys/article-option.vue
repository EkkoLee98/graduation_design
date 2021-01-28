<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="作者关联ID" label-width="100px" prop="authorId">
      <el-input v-model="dataForm.authorId" placeholder="作者关联ID"></el-input>
    </el-form-item>
    <el-form-item label="浏览量" label-width="100px" prop="browseCount">
      <el-input v-model="dataForm.browseCount" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="类型" label-width="100px" prop="classify">
      <el-input v-model="dataForm.classify" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="收藏数量" label-width="100px" prop="collectionCount">
      <el-input v-model="dataForm.collectionCount" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="评论数量" label-width="100px" prop="commentsCount">
      <el-input v-model="dataForm.commentsCount" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="内容" prop="content">
      <el-input v-model="dataForm.content" placeholder=""></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
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
          id: 0,
          authorId: '',
          browseCount: '',
          classify: '',
          collectionCount: '',
          commentsCount: '',
          content: ''
        },
        dataRule: {
          authorId: [
            { required: true, message: '作者关联ID不能为空', trigger: 'blur' }
          ],
          browseCount: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          classify: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          collectionCount: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          commentsCount: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          content: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/sys/articleauthor/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.authorId = data.articleAuthor.authorId
                this.dataForm.browseCount = data.articleAuthor.browseCount
                this.dataForm.classify = data.articleAuthor.classify
                this.dataForm.collectionCount = data.articleAuthor.collectionCount
                this.dataForm.commentsCount = data.articleAuthor.commentsCount
                this.dataForm.content = data.articleAuthor.content
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
              url: this.$http.adornUrl(`/sys/articleauthor/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'authorId': this.dataForm.authorId,
                'browseCount': this.dataForm.browseCount,
                'classify': this.dataForm.classify,
                'collectionCount': this.dataForm.collectionCount,
                'commentsCount': this.dataForm.commentsCount,
                'content': this.dataForm.content
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
