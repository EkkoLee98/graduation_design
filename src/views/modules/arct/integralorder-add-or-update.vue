<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="" prop="goodName">
      <el-input v-model="dataForm.goodName" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="authorName">
      <el-input v-model="dataForm.authorName" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="integral">
      <el-input v-model="dataForm.integral" placeholder=""></el-input>
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
          goodName: '',
          authorName: '',
          integral: ''
        },
        dataRule: {
          goodName: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          authorName: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          integral: [
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
              url: this.$http.adornUrl(`/arct/integralorder/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.goodName = data.integralOrder.goodName
                this.dataForm.authorName = data.integralOrder.authorName
                this.dataForm.integral = data.integralOrder.integral
                this.dataForm.address = data.integralOrder.address
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
              url: this.$http.adornUrl(`/arct/integralorder/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'goodName': this.dataForm.goodName,
                'authorName': this.dataForm.authorName,
                'integral': this.dataForm.integral,
                'address': this.dataForm.address
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
