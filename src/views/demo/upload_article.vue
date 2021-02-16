<template>
  <div id="fulltext">
    <el-upload
      id="quill-upload"
      class="avatar-uploader"
      action="http://localhost:8080/renren-fast/api/upload"
      :show-file-list="false"
      :headers="token"
      :on-success="uploadSuccess"
      :on-error="uploadError"
      :before-upload="beforeUpload"

    >
    </el-upload>

    <!-- <button @click="myclick">test</button> -->
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" label-width="80px">
      <el-form-item label="标题" prop="title">
        <el-input v-model="dataForm.title" placeholder=""></el-input>
      </el-form-item>
      <el-form-item v-if="isAdmin && !isAdd" label="浏览数量" prop="browseCount">
        <el-input v-model="dataForm.browseCount" placeholder=""></el-input>
      </el-form-item>
      <el-form-item label="类别" prop="classify">
        <el-select v-model="dataForm.classify" placeholder="请选择类别">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="内容" prop="content">
        <el-row v-loading="quillUpdateImg">
          <quill-editor
            v-model="dataForm.content"
            ref="myQuillEditor"
            :options="editorOption"
            @change="onEditorChange($event)"
            @blur="onEditorBlur($event)"
            @focus="onEditorFocus($event)"
            @ready="onEditorReady($event)"
          >
          </quill-editor>
        </el-row>
      </el-form-item>
      <el-form-item v-if="isAdmin && !isAdd" label="点赞数" prop="thumbsUpCount">
        <el-input v-model="dataForm.thumbsUpCount" placeholder=""></el-input>
      </el-form-item>
      <el-form-item label="展示" prop="mode">
<!--        <el-input v-model="dataForm.mode" placeholder=""></el-input>-->
        <el-radio-group v-model="dataForm.mode">
          <el-radio label="base">普通模式</el-radio>
          <el-radio label="column">多图模式</el-radio>
          <el-radio label="image">大图模式</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item v-if="isAdmin && !isAdd" label="收藏数" prop="collectionCount">
        <el-input v-model="dataForm.collectionCount" placeholder=""></el-input>
      </el-form-item>
      <el-form-item label="封面图片" prop="cover">
        <el-upload
          class="upload-demo"
          action="http://localhost:8080/renren-fast/api/upload"
          :on-preview="handlePreviewCover"
          :on-remove="handleRemoveCoer"
          :before-remove="beforeRemoveCover"
          :on-success="uploadCoverSuccess"
          :on-error="uploadError"
          :before-upload="beforeCoverUpload"
          :headers="token"
          multiple
          :limit="3"
          :on-exceed="beforeCoverUpload"
          :file-list="dataForm.cover">
          <el-button :loading="uploading" size="small" type="primary">点击上传</el-button>
          <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
        </el-upload>
      </el-form-item>
    </el-form>
    <!--富文本编辑器组件-->
    <div style="margin: 0 auto;width: 200px">
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
      <el-button style="margin-left: 50px" @click="this.$router.go(-1)">取消</el-button>
    </div>
<!--    <div v-html="content" style="width: 500px;height: 500px"></div>-->

    <!-- <div v-html="this.content">{{this.content}}</div> -->
  </div>
</template>

<script>
// import Quill from 'quill';
// import { ImageResize } from '../../image-resize.min.js'
import {Quill} from 'vue-quill-editor'
import {debounce} from '../../utils/index'
import {ImageDrop} from 'quill-image-drop-module'
import ImageResize from 'quill-image-resize-module'

Quill.register('modules/imageDrop', ImageDrop)
Quill.register('modules/imageResize', ImageResize)
const toolbarOptions = [
  ['bold', 'italic', 'underline', 'strike'], // 加粗 斜体 下划线 删除线
  ['blockquote', 'code-block'], // 引用  代码块
  [{header: 1}, {header: 2}], // 1、2 级标题
  [{list: 'ordered'}, {list: 'bullet'}], // 有序、无序列表
  [{script: 'sub'}, {script: 'super'}], // 上标/下标
  [{indent: '-1'}, {indent: '+1'}], // 缩进
  // [{'direction': 'rtl'}],                         // 文本方向
  [{size: ['small', false, 'large', 'huge']}], // 字体大小
  [{header: [1, 2, 3, 4, 5, 6, false]}], // 标题
  [{color: []}, {background: []}], // 字体颜色、字体背景颜色
  [{font: []}], // 字体种类
  [{align: []}], // 对齐方式
  ['clean'], // 清除文本格式
  ['link', 'image', 'video'] // 链接、图片、视频
]
export default {
  name: 'Home',
  data () {
    return {
      timer: null,
      inter: null,
      quillUpdateImg: false,
      uploading: false,
      options: [{
        value: '每周推荐',
        label: '每周推荐'
      }, {
        value: '最近热销',
        label: '最近热销'
      }, {
        value: '发烧硬件',
        label: '发烧硬件'
      }, {
        value: '王牌栏目',
        label: '王牌栏目'
      }, {
        value: '推友分享',
        label: '推友分享'
      }, {
        value: '模拟装机',
        label: '模拟装机'
      }],
      dataForm: {
        id: 0,
        authorId: '',
        browseCount: '0',
        classify: '',
        content: '',
        thumbsUpCount: '0',
        title: '',
        mode: '',
        createTime: '',
        collectionCount: '0',
        cover: []
      },
      dataRule: {
        classify: [
          { required: true, message: '不能为空', trigger: 'change' }
        ],
        content: [
          { required: true, message: '不能为空', trigger: 'blur' }
        ],
        mode: [
          { required: true, message: '不能为空', trigger: 'change' }
        ],
        title: [
          { required: true, message: '不能为空', trigger: 'blur' }
        ]
      },
      headerToken: {'token': this.$cookie.get('token')},
      actionUrl: process.env.VUE_APP_URL + '/api/upload',
      // 富文本内容
      editorOption: {
        // some quill options
        modules: {

          imageDrop: true,      // 图片拖拽
          imageResize: {
            displayStyles: {
              backgroundColor: 'black',
              border: 'none',
              color: 'white'
            },
            modules: ['Resize', 'DisplaySize', 'Toolbar']
          },
          toolbar: {
            container: toolbarOptions,  // 工具栏
            handlers: {
              'image': function (value) {
                if (value) {
                  // alert(value)
                  // alert(document.querySelector('#quill-upload'))
                  document.querySelector('#quill-upload input').click()
                } else {
                  this.quill.format('image', false)
                }
              }
            }
          }// 工具菜单栏配置
        }
      }
    }
  },
  // watch: {
  //   content: {
  //     handler (val) {
  //       console.log(val)
  //     },
  //     immediate: true
  //   }
  // },
  methods: {
    // test (e, p) {
    //   console.log(e)
    //   console.log(p)
    // },
    init () {
      if (this.$route.params.option === 'add') {
        this.dataForm.id = 0
      } else {
        this.dataForm.id = this.$route.params.option
      }
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.id) {
          this.$http({
            url: this.$http.adornUrl(`/arct/article/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({data}) => {
            if (data && data.code === 0) {
              // let str = "'" + data.article.cover + "'"
              // console.log(str)
              // let arrString = '["陕西省", "西安市", "高新区"]'
              // let objArray = eval(arrString)
              // console.log(objArray)  //  ["陕西省", "西安市", "高新区"]
              // console.log(eval('(' + str + ')'))
              this.dataForm.authorId = data.article.authorId
              this.dataForm.browseCount = data.article.browseCount
              this.dataForm.classify = data.article.classify
              this.dataForm.content = data.article.content
              this.dataForm.thumbsUpCount = data.article.thumbsUpCount
              this.dataForm.title = data.article.title
              this.dataForm.mode = data.article.mode
              this.dataForm.createTime = data.article.createTime
              this.dataForm.collectionCount = data.article.collectionCount
              // eslint-disable-next-line no-eval
              this.dataForm.cover = JSON.parse(data.article.cover)
            }
          })
        }
      })
    },
    // 表单提交
    dataFormSubmit () {
      if (this.dataForm.mode !== '') {
        if (this.dataForm.mode === 'column') {
          if (this.dataForm.cover.length === 0) {
            this.$message.error('至少选择一张图片')
            return false
          }
          if (this.dataForm.cover.length < 3) {
            this.$message.error('多图模式要选择3长图片')
            return false
          }
        } else if (this.dataForm.cover.length === 0) {
          this.$message.error('至少选择一张图片')
          return false
        }
      }
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(`/arct/article/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'authorId': JSON.parse(this.$cookie.get('author')).id,
              'browseCount': this.dataForm.browseCount,
              'classify': this.dataForm.classify,
              'content': this.dataForm.content,
              'thumbsUpCount': this.dataForm.thumbsUpCount,
              'title': this.dataForm.title,
              'mode': this.dataForm.mode,
              'createTime': new Date().getTime(),
              'collectionCount': this.dataForm.collectionCount,
              'cover': JSON.stringify(this.dataForm.cover)
            })
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.$router.push({path: '/arct-article'})
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        }
      })
    },
    //  测试子组件向父组件传值
    //  myclick(){
    //   console.log("子容器里的方法");
    //    this.$emit("func","hah")
    //  },
    onEditorBlur (quill) { // 可以不用了
      // 保存到本地,这种图片有点bug,因为这时是使用el-upload组件的，如果调大小就会失去焦点，然后只会保存调大小之前的，先暂时不管
      // localStorage.setItem("content",JSON.stringify(this.content))

      // console.log('editor blur!', quill)
      // 保存完就发送给父组件的表单
      this.timer = setTimeout(() => {
        // 给10s钟调整图片大小，因为怎么说，点击图片就已经离开了焦点
        localStorage.setItem('content', this.dataForm.content)
        // alert("将自动保存到本地")
      }, 10000)
      // this.$emit('func', this.content)
      // console.log(this.content);
    },
    onEditorFocus (quill) {
      clearTimeout(this.timer)
      this.cacheContent()
      console.log('editor focus!', quill)
    },
    onEditorReady (quill) {
      console.log('editor ready!', quill)
    },
    onEditorChange ({quill, html, text}) {
      // console.log('editor change!', quill, html, text)
      this.dataForm.content = html
      debounce(() => {
        this.cacheContent()
      }, 1000)
    },
    cacheContent () {
      console.log('触发')
      localStorage.setItem('content', this.dataForm.content)
    },
    // 上传图片前
    beforeUpload (res, file) {
      this.quillUpdateImg = true
    },
    beforeCoverUpload (file) {
      this.uploading = true
      if (this.dataForm.cover.length === 3) {
        this.$message.error('最多上传三张！')
        this.uploading = false
        return false
      }
      // const isJPG = file.type === 'image/jpeg'
      // const isLt2M = file.size / 1024 / 1024 < 2

      // if (!isJPG) {
      //   this.uploading = false
      //   this.$message.error('上传头像图片只能是 JPG 格式!')
      // }
      // if (!isLt2M) {
      //   this.uploading = false
      //   this.$message.error('上传头像图片大小不能超过 2MB!')
      // }
      // return isJPG
    },
    uploadCoverSuccess (res) {
      this.uploading = false
      let tmpObj = {'name': res.name, 'url': res.url}
      this.dataForm.cover.push(tmpObj)
      console.log(this.dataForm.cover)
      console.log(JSON.stringify(this.dataForm.cover))
    },
    uploadCoverError () {
      this.uploading = false
      this.$message.error('上传失败！')
    },
    beforeRemoveCover (file, fileList) {
      return this.$confirm(`确定移除 ${file.name}？`)
    },
    handleRemoveCoer (file, fileList) {
      let idx = this.dataForm.cover.findIndex(f => f.name === file.name)
      this.dataForm.cover.splice(idx, 1)
      console.log(this.dataForm.cover)
    },
    handlePreviewCover (file) {
      console.log(file)
    },
    // 上传图片成功
    async uploadSuccess (res, file) {
      // res为图片服务器返回的数据
      // 获取富文本组件实例
      let quill = this.$refs.myQuillEditor.quill

      // 如果上传成功
      if (res.code === 1) {
        // 获取光标所在位置
        let length = quill.getSelection().index
        // 插入图片  res.info为服务器返回的图片地址
        console.log(res)
        // const img = "<img src='" + res.url + "'/>"
        quill.insertEmbed(length, 'image', res.url)
        // 调整光标到最后
        quill.setSelection(length + 1)
      } else {
        this.$message.error('图片插入失败1')
      }
      // loading动画消失
      this.quillUpdateImg = false
    },
    // 上传图片失败
    uploadError (res, file) {
      // alert(res.code)
      // loading动画消失
      this.quillUpdateImg = false
      this.$message.error('图片插入失败2')
    }
  },
  created () {
    this.$nextTick(() => {
      console.log(localStorage.getItem('content'))
      if (this.$route.params.option === 'add') {
        this.dataForm.content = localStorage.getItem('content')
      } else {
        this.init()
      }
      console.log(this.$route.params)
      console.log(JSON.parse(this.$cookie.get('author')))
      console.log(JSON.parse(this.$cookie.get('role')))
      // this.inter = setInterval(() => {
      //   console.log('========auto save=========')
      //   this.content = JSON.parse(localStorage.getItem('content'))
      // }, 5000)
    })
  },
  computed: {
    editor () {
      return this.$refs.myQuillEditor.quill
    },
    token () {
      return {'token': this.$cookie.get('token')}
    },
    isAdd () {
      return this.$route.params.option === 'add'
    },
    isAdmin () {
      return JSON.parse(this.$cookie.get('role')).roleId === 1
    }
  },
  mounted () {
    console.log('this is current quill instance object', this.editor)
    console.log(this.isAdmin)
  }
}
</script>
<style>
#fulltext {
  width: 700px;
  height: 300px;
}
.ql-editor {
  height: 300px;
}
</style>
