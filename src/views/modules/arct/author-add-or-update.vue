<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="" prop="authorName">
      <el-input v-model="dataForm.authorName" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="avatar">
      <el-input v-model="dataForm.avatar" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="status">
      <el-input v-model="dataForm.status" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="articleIds">
      <el-input v-model="dataForm.articleIds" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="articleLikesIds">
      <el-input v-model="dataForm.articleLikesIds" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="authorLikesIds">
      <el-input v-model="dataForm.authorLikesIds" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="explain">
      <el-input v-model="dataForm.explain" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="fansCount">
      <el-input v-model="dataForm.fansCount" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="followCount">
      <el-input v-model="dataForm.followCount" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="gender">
      <el-input v-model="dataForm.gender" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="integralCount">
      <el-input v-model="dataForm.integralCount" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="professional">
      <el-input v-model="dataForm.professional" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="thumpsUpArticleIds">
      <el-input v-model="dataForm.thumpsUpArticleIds" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="labelIds">
      <el-input v-model="dataForm.labelIds" placeholder=""></el-input>
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
          authorName: '',
          avatar: '',
          status: '',
          articleIds: '',
          articleLikesIds: '',
          authorLikesIds: '',
          explain: '',
          fansCount: '',
          followCount: '',
          gender: '',
          integralCount: '',
          professional: '',
          thumpsUpArticleIds: '',
          labelIds: ''
        },
        dataRule: {
          authorName: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          avatar: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          articleIds: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          articleLikesIds: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          authorLikesIds: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          explain: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          fansCount: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          followCount: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          gender: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          integralCount: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          professional: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          thumpsUpArticleIds: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          labelIds: [
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
              url: this.$http.adornUrl(`/arct/author/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.authorName = data.author.authorName
                this.dataForm.avatar = data.author.avatar
                this.dataForm.status = data.author.status
                this.dataForm.articleIds = data.author.articleIds
                this.dataForm.articleLikesIds = data.author.articleLikesIds
                this.dataForm.authorLikesIds = data.author.authorLikesIds
                this.dataForm.explain = data.author.explain
                this.dataForm.fansCount = data.author.fansCount
                this.dataForm.followCount = data.author.followCount
                this.dataForm.gender = data.author.gender
                this.dataForm.integralCount = data.author.integralCount
                this.dataForm.professional = data.author.professional
                this.dataForm.thumpsUpArticleIds = data.author.thumpsUpArticleIds
                this.dataForm.labelIds = data.author.labelIds
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
              url: this.$http.adornUrl(`/arct/author/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'authorName': this.dataForm.authorName,
                'avatar': this.dataForm.avatar,
                'status': this.dataForm.status,
                'articleIds': this.dataForm.articleIds,
                'articleLikesIds': this.dataForm.articleLikesIds,
                'authorLikesIds': this.dataForm.authorLikesIds,
                'explain': this.dataForm.explain,
                'fansCount': this.dataForm.fansCount,
                'followCount': this.dataForm.followCount,
                'gender': this.dataForm.gender,
                'integralCount': this.dataForm.integralCount,
                'professional': this.dataForm.professional,
                'thumpsUpArticleIds': this.dataForm.thumpsUpArticleIds,
                'labelIds': this.dataForm.labelIds
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
