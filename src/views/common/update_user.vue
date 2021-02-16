<template>
  <div>
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="110px">
      <el-form-item label="名字" prop="authorName">
        <el-input v-model="dataForm.authorName" placeholder=""></el-input>
      </el-form-item>
      <el-form-item label="自我介绍" prop="motto">
        <el-input v-model="dataForm.motto" placeholder=""></el-input>
      </el-form-item>
      <el-form-item label="头像" prop="avatar">
        <el-upload
          class="avatar-uploader"
          action="http://localhost:8080/renren-fast/api/upload"
          :show-file-list="false"
          :headers="token"
          :on-success="handleAvatarSuccess"
          :before-upload="beforeAvatarUpload">
          <img v-if="dataForm.avatar" :src="dataForm.avatar" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="性别" prop="gender">
        <el-radio-group v-model="dataForm.gender">
          <el-radio label="男">男</el-radio>
          <el-radio label="女">女</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="职业" prop="professional">
        <el-input v-model="dataForm.professional" placeholder=""></el-input>
      </el-form-item>
      <el-form-item label="地址" prop="address">
        <el-input v-model="dataForm.address" placeholder=""></el-input>
      </el-form-item>
    </el-form>
    <div style="width: 200px;margin: 0 auto">
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      dataForm: {
        id: 0,
        authorName: '',
        avatar: '',
        motto: '',
        gender: '',
        professional: '',
        address: ''
      },
      dataRule: {
        authorName: [
          { required: true, message: '不能为空', trigger: 'blur' }
        ],
        motto: [
          { required: true, message: '不能为空', trigger: 'blur' }
        ],
        gender: [
          { required: true, message: '不能为空', trigger: 'change' }
        ],
        professional: [
          { required: true, message: '不能为空', trigger: 'blur' }
        ],
        address: [
          { required: true, message: '不能为空', trigger: 'blur' }
        ]
      }
    }
  },
  mounted () {
    this.init()
  },
  computed: {
    token () {
      return {'token': this.$cookie.get('token')}
    }
  },
  methods: {
    init () {
      this.dataForm.id = this.$route.query.id
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
              this.dataForm.motto = data.author.motto
              this.dataForm.avatar = data.author.avatar
              this.dataForm.gender = data.author.gender
              this.dataForm.professional = data.author.professional
              this.dataForm.address = data.author.address
            }
          })
        }
      })
    },
    handleAvatarSuccess (res, file) {
      this.dataForm.avatar = res.url
    },
    beforeAvatarUpload (file) {
      const isJPG = file.type === 'image/jpeg'
      const isLt2M = file.size / 1024 / 1024 < 2

      if (!isJPG) {
        this.$message.error('上传头像图片只能是 JPG 格式!')
      }
      if (!isLt2M) {
        this.$message.error('上传头像图片大小不能超过 2MB!')
      }
      return isJPG && isLt2M
    },
    // 表单提交
    dataFormSubmit () {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(`/arct/author/update`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.$route.query.id,
              'authorName': this.dataForm.authorName,
              'avatar': this.dataForm.avatar,
              'motto': this.dataForm.motto,
              'gender': this.dataForm.gender,
              'professional': this.dataForm.professional,
              'address': this.dataForm.address
            })
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message.success('修改成功！')
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
<style lang="scss" scoped>
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409EFF;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}
.avatar {
  width: 178px;
  height: 178px;
  display: block;
}
</style>
