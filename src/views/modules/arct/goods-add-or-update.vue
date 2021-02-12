<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="" prop="goodName">
      <el-input v-model="dataForm.goodName" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="goodPrice">
      <el-input v-model="dataForm.goodPrice" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="goodImg">
      <el-input v-model="dataForm.goodImg" placeholder=""></el-input>
    </el-form-item>
      <el-form-item label="" prop="goodIntegral">
        <el-input v-model="dataForm.goodIntegral" placeholder=""></el-input>
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
          goodId: 0,
          goodName: '',
          goodPrice: '',
          goodImg: '',
          goodIntegral: ''
        },
        dataRule: {
          goodName: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          goodPrice: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          goodImg: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.goodId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.goodId) {
            this.$http({
              url: this.$http.adornUrl(`/arct/goods/info/${this.dataForm.goodId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.goodName = data.goods.goodName
                this.dataForm.goodPrice = data.goods.goodPrice
                this.dataForm.goodImg = data.goods.goodImg
                this.dataForm.goodIntegral = data.goods.goodIntegral
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
              url: this.$http.adornUrl(`/arct/goods/${!this.dataForm.goodId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'goodId': this.dataForm.goodId || undefined,
                'goodName': this.dataForm.goodName,
                'goodPrice': this.dataForm.goodPrice,
                'goodImg': this.dataForm.goodImg,
                'goodIntegral': this.dataForm.goodIntegral
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
