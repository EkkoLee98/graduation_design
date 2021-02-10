<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="作者关联ID" prop="authorId">
      <el-input v-model="dataForm.authorId" placeholder="作者关联ID"></el-input>
    </el-form-item>
    <el-form-item label="" prop="browseCount">
      <el-input v-model="dataForm.browseCount" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="classify">
      <el-input v-model="dataForm.classify" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="content">
      <el-input v-model="dataForm.content" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="thumbsUpCount">
      <el-input v-model="dataForm.thumbsUpCount" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="title">
      <el-input v-model="dataForm.title" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="mode">
      <el-input v-model="dataForm.mode" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="collectionCount">
      <el-input v-model="dataForm.collectionCount" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="cover">
      <el-input v-model="dataForm.cover" placeholder=""></el-input>
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
          content: '',
          thumbsUpCount: '',
          title: '',
          mode: '',
          createTime: '',
          collectionCount: '',
          cover: ''
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
          content: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          thumbsUpCount: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          title: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          mode: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          collectionCount: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          cover: [
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
              url: this.$http.adornUrl(`/arct/article/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.authorId = data.article.authorId
                this.dataForm.browseCount = data.article.browseCount
                this.dataForm.classify = data.article.classify
                this.dataForm.content = data.article.content
                this.dataForm.thumbsUpCount = data.article.thumbsUpCount
                this.dataForm.title = data.article.title
                this.dataForm.mode = data.article.mode
                this.dataForm.createTime = data.article.createTime
                this.dataForm.collectionCount = data.article.collectionCount
                this.dataForm.cover = data.article.cover
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
              url: this.$http.adornUrl(`/arct/article/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'authorId': this.dataForm.authorId,
                'browseCount': this.dataForm.browseCount,
                'classify': this.dataForm.classify,
                'content': this.dataForm.content,
                'thumbsUpCount': this.dataForm.thumbsUpCount,
                'title': this.dataForm.title,
                'mode': this.dataForm.mode,
                'createTime': this.dataForm.createTime,
                'collectionCount': this.dataForm.collectionCount,
                'cover': this.dataForm.cover
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
