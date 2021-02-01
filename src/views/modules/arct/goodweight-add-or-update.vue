<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="主板权重" prop="mainBoard">
      <el-input v-model="dataForm.mainBoard" placeholder="主板权重"></el-input>
    </el-form-item>
    <el-form-item label="cpu权重" prop="cpu">
      <el-input v-model="dataForm.cpu" placeholder="cpu权重"></el-input>
    </el-form-item>
    <el-form-item label="显卡权重" prop="graphicsCard">
      <el-input v-model="dataForm.graphicsCard" placeholder="显卡权重"></el-input>
    </el-form-item>
    <el-form-item label="内存权重" prop="memory">
      <el-input v-model="dataForm.memory" placeholder="内存权重"></el-input>
    </el-form-item>
    <el-form-item label="电源权重" prop="powerSupply">
      <el-input v-model="dataForm.powerSupply" placeholder="电源权重"></el-input>
    </el-form-item>
    <el-form-item label="是否使用此套权重" prop="usingStatus">
      <el-input v-model="dataForm.usingStatus" placeholder="是否使用此套权重"></el-input>
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
          mainBoard: '',
          cpu: '',
          graphicsCard: '',
          memory: '',
          powerSupply: '',
          usingStatus: ''
        },
        dataRule: {
          mainBoard: [
            { required: true, message: '主板权重不能为空', trigger: 'blur' }
          ],
          cpu: [
            { required: true, message: 'cpu权重不能为空', trigger: 'blur' }
          ],
          graphicsCard: [
            { required: true, message: '显卡权重不能为空', trigger: 'blur' }
          ],
          memory: [
            { required: true, message: '内存权重不能为空', trigger: 'blur' }
          ],
          powerSupply: [
            { required: true, message: '电源权重不能为空', trigger: 'blur' }
          ],
          usingStatus: [
            { required: true, message: '是否使用此套权重不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/arct/goodweight/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.mainBoard = data.goodWeight.mainBoard
                this.dataForm.cpu = data.goodWeight.cpu
                this.dataForm.graphicsCard = data.goodWeight.graphicsCard
                this.dataForm.memory = data.goodWeight.memory
                this.dataForm.powerSupply = data.goodWeight.powerSupply
                this.dataForm.usingStatus = data.goodWeight.usingStatus
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
              url: this.$http.adornUrl(`/arct/goodweight/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'mainBoard': this.dataForm.mainBoard,
                'cpu': this.dataForm.cpu,
                'graphicsCard': this.dataForm.graphicsCard,
                'memory': this.dataForm.memory,
                'powerSupply': this.dataForm.powerSupply,
                'usingStatus': this.dataForm.usingStatus
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
