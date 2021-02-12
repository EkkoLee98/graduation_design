<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="评论" prop="commentContent">
      <el-input v-model="dataForm.commentContent" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="是否子回复" prop="isReply">
      <el-input v-model="dataForm.isReply" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="发表人" prop="authorId">
      <el-input v-model="dataForm.authorId" placeholder=""></el-input>
    </el-form-item>
      <el-form-item label="文章id" prop="articleId">
        <el-input v-model="dataForm.articleId" placeholder=""></el-input>
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
          commentContent: '',
          createTime: '',
          isReply: '',
          authorId: '',
          articleId: ''
        },
        dataRule: {
          commentContent: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          authorId: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          articleId: [
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
              url: this.$http.adornUrl(`/arct/comments/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.commentContent = data.comments.commentContent
                this.dataForm.createTime = data.comments.createTime
                this.dataForm.isReply = data.comments.isReply
                this.dataForm.authorId = data.comments.authorId
                this.dataForm.articleId = data.comments.articleId
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
              url: this.$http.adornUrl(`/arct/comments/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'commentContent': this.dataForm.commentContent,
                'createTime': new Date().getTime(),
                'isReply': '0',
                'authorId': this.dataForm.authorId,
                'articleId': this.dataForm.articleId
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
