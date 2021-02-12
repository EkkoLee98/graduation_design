<template>
  <div>
    <div class="main">
      <div class="img-area">
        <div style="margin-bottom: 50px;border-bottom: 1px solid #ebeef5">
          <swiper :options="swiperOptionTop" class="gallery-top imgs" ref="swiperTop">
            <swiper-slide class="slide-1 imgsList" v-for="(item,index) in ImgArr" :key="index">
              <img :src="item" alt="">
            </swiper-slide>
            <div class="swiper-button-next swiper-button-black" slot="button-next"></div>
            <div class="swiper-button-prev swiper-button-black" slot="button-prev"></div>
          </swiper>
          <!-- swiper2 Thumbs -->
          <swiper :options="swiperOptionThumbs" class="gallery-thumbs" ref="swiperThumbs">
            <swiper-slide class="slide-1" v-for="(item,index) in ImgArr" :key="index">
              <img :src="item" alt="">
            </swiper-slide>
          </swiper>
        </div>
        <div class="buttom-area">
          <el-card class="box-card">
            <div slot="header" class="clearfix">
              <span>推荐原因</span>
              <el-button style="float: right; padding: 3px 0" type="text">商品简介</el-button>
            </div>
            <div class="text item">
              <div style="margin: 10px 0 10px 0">
                超好的包包超好的包包超好的包包 辣妹必备 超哥必备
              </div>
              <div style="margin: 20px 0 20px 0">
                超好的包包超好的包包超好的包包 超哥代言必属精品 啊啊啊啊啊我要死了
              </div>
              <div class="color-8a share" style="margin: 20px 0 20px 0">

                <el-tooltip  effect="light" :content="goodObj.commodityInfo" placement="top">
                  <div class="goods-name">商品名字：{{goodObj.commodityName}}</div>
                </el-tooltip>
                <div class="vLine"></div>
                一起来分享给朋友看看吧：
                <i @click="goQQ" class="icon iconfont icon-qq"></i>
                <i @click="goWX" class="icon iconfont icon-weixin"></i>
                <i @click="goWB" class="icon iconfont icon-weibo"></i>
              </div>
            </div>
          </el-card>
        </div>
      </div>
      <div class="about">
        <el-card class="box-card">
          <div class="head">
            <img :src="ImgArr[0]" alt="">
            <span class="good-name">品牌: {{goodObj.brand}}</span>
            <el-button @click="collectGood" :disabled="goodObj.collect" style="margin-left: 40px" type="text">{{goodObj.collect ? '已收藏' : '收藏'}}</el-button>
          </div>
          <div class="footer">
            <div class="foot-content left">
              <div class="color-8a">分享</div>
              <div>477</div>
            </div>
            <div class="foot-content border">
              <div class="color-8a">浏览</div>
              <div>5.5万</div>
            </div>
            <div class="foot-content right">
              <div class="color-8a">获赞与收藏</div>
              <div>{{goodObj.collectCount + goodObj.likeCount}}</div>
            </div>
          </div>
        </el-card>
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <span>相关标签</span>
            <!--            <el-button style="float: right; padding: 3px 0" type="text">操作按钮</el-button>-->
          </div>
          <el-tag
            v-for="tag in tags"
            :class="{'active': tag.active}"
            :key="tag.name"
            :type="tag.type">
            {{ tag.name }}
          </el-tag>
        </el-card>
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <span>相关推荐</span>
            <!--            <el-button style="float: right; padding: 3px 0" type="text">操作按钮</el-button>-->
          </div>
          <div class="box" v-for="(item, index) in recommended" :key="index">
            <img class="box-img" :src="item.img">
            <div class="box-content">
              <div class="title">
                {{ item.title }}
              </div>
              <div class="like" :class="{'liked': item.like === true}" @click="likeIt(item)">
                <i :class="[item.like ? yes : no]"></i>
                <span>{{ item.num }}</span>
              </div>
            </div>
          </div>
        </el-card>
      </div>
    </div>
    <div class="main-footer">
      <el-tabs v-model="activeName" @tab-click="handleClick">
        <el-tab-pane label="商品详细" name="first">
          <div class="title">
            品牌名称: <span style="color: black">{{goodObj.brand}}</span>
          </div>
          <div class="title">
            品牌参数:
          </div>
          <div class="content">
            <div class="tool-box">
              <el-tooltip class="item" effect="light" content="4354354354354534354354354354354354354"
                          placement="top-start">
                <span>品牌: 4354354354354534354354354354354354354</span>
              </el-tooltip>
              <el-tooltip class="item" effect="light" content="4354354354354534354354354354354354354"
                          placement="top-start">
                <span>鞋码: 4354354354354534354354354354354354354</span>
              </el-tooltip>
              <el-tooltip class="item" effect="light" content="4354354354354534354354354354354354354"
                          placement="top-start">
                <span>颜色分类: 4354354354354534354354354354354354354</span>
              </el-tooltip>
            </div>
          </div>
          <div class="content">
            <div class="tool-box">
              <el-tooltip class="item" effect="light" content="4354354354354534354354354354354354354"
                          placement="top-start">
                <span>吊牌: 4354354354354534354354354354354354354</span>
              </el-tooltip>
              <el-tooltip class="item" effect="light" content="4354354354354534354354354354354354354"
                          placement="top-start">
                <span>款号: 4354354354354534354354354354354354354</span>
              </el-tooltip>
              <el-tooltip class="item" effect="light" content="4354354354354534354354354354354354354"
                          placement="top-start">
                <span>上市时间: 4354354354354534354354354354354354354</span>
              </el-tooltip>
            </div>
          </div>
          <div class="content">
            <div class="tool-box">
              <el-tooltip class="item" effect="light" content="4354354354354534354354354354354354354"
                          placement="top-start">
                <span>高度: 4354354354354534354354354354354354354</span>
              </el-tooltip>
              <el-tooltip class="item" effect="light" content="4354354354354534354354354354354354354"
                          placement="top-start">
                <span>性别: 4354354354354534354354354354354354354</span>
              </el-tooltip>
              <el-tooltip class="item" effect="light" content="4354354354354534354354354354354354354"
                          placement="top-start">
                <span>是否商城同款: 4354354354354534354354354354354354354</span>
              </el-tooltip>
            </div>
          </div>
        </el-tab-pane>
        <el-tab-pane label="累计评价" name="second">
          <div class="menu-wrap">
            <div class="comment" v-for="(item, index) in commentList">
              <div style="margin-right: 20px">
                <img src="https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=1374152916,1336053607&fm=26&gp=0.jpg">
              </div>
              <div style="text-align: left">
                <div style="margin-bottom: 10px;font-size: 12px;color: #898989">{{item.userName}}</div>
                <div style="margin-bottom: 10px">{{item.evaluation}}</div>
                <div style="margin-bottom: 10px;font-size: 12px;color: #898989">{{item.createTime}}</div>
              </div>
            </div>
            <el-button style="margin-top: 10px; float: left" type="primary" @click="dialogFormVisible = true">发表评论</el-button>
          </div>
        </el-tab-pane>
      </el-tabs>
    </div>
    <el-dialog title="发表评论" :visible.sync="dialogFormVisible">
      <el-form :model="form">
        <el-form-item label="评论" :label-width="formLabelWidth">
          <el-input v-model="form.evaluation" auto-complete="off"></el-input>
        </el-form-item>
        <el-form-item label="星星" :label-width="formLabelWidth">
          <el-rate :max="5" style="margin-top: 6px" v-model="form.star"></el-rate>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="submitComment()">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'store',
  data () {
    return {
      commentList: [{
        'userName': 'test',
        'evaluation': '超哥帅',
        'createTime': '2077-01-01'
      }],
      dialogFormVisible: false,
      form: {
        evaluation: '',
        star: null
      },
      formLabelWidth: '120px',
      tags: [
        {name: '时尚', type: 'info', active: true},
        {name: '箱包', type: 'info', active: true},
        {name: '饰品', type: 'info', active: false},
        {name: 'kvk', type: 'info', active: false},
        {name: '百搭包包', type: 'info', active: false},
        {name: '标拍照', type: 'info', active: false},
        {name: '夏天', type: 'info', active: false},
        {name: '收纳', type: 'info', active: false},
        {name: '艺术感', type: 'info', active: false},
        {name: '肩带', type: 'info', active: false},
        {name: '搭配', type: 'info', active: false}
      ],
      recommended: [
        {
          title: '2020年花16万买包 有满足也有候会',
          like: false,
          num: 30,
          img: 'https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=64754120,2926909516&fm=26&gp=0.jpg'
        },
        {
          title: '这只奶油白有点绝！就是这么丝滑~',
          like: false,
          num: 115,
          img: 'https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=1757638012,1516051550&fm=26&gp=0.jpg'
        },
        {
          title: '香奈儿流浪包黑金小号菱格 呜呼呼~',
          like: true,
          num: 20,
          img: 'https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=122592915,2313113112&fm=26&gp=0.jpg'
        },
        {
          title: '发现了几家宝宝的宝藏店铺，必须要跟姐妹们分享',
          like: false,
          num: 310,
          img: 'https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=64754120,2926909516&fm=26&gp=0.jpg'
        }
      ],
      ImgArr: [
        'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fzkres2.myzaker.com%2F202004%2F5ea295f4622768f61400027b_1024.jpg&refer=http%3A%2F%2Fzkres2.myzaker.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1614627895&t=2054eff631947a222ab8f845cd7bc770'
      ],
      goodObj: {},
      reset: true,
      swiperOptionTop: {
        effect: 'fade',
        orderType: 0,
        spaceBetween: 10,
        navigation: {
          nextEl: '.swiper-button-next',
          prevEl: '.swiper-button-prev'
        }
      },
      swiperOptionThumbs: {
        spaceBetween: 10,
        centeredSlides: true,
        slidesPerView: 'auto',
        touchRatio: 0.2,
        slideToClickedSlide: true,
        // autoplay:true
        autoplay: {
          delay: 4000,
          speed: 1500,
          disableOnInteraction: true
        }
      },
      swiperSlides: [],
      yes: 'el-icon-star-on',
      no: 'el-icon-star-off',
      activeName: 'first'
    }
  },
  created () {
    this.$nextTick(() => {
      const swiperTop = this.$refs.swiperTop.swiper
      const swiperThumbs = this.$refs.swiperThumbs.swiper
      swiperTop.controller.control = swiperThumbs
      swiperThumbs.controller.control = swiperTop
    })
    console.log(this.$route.query)
    this.getGoodDetail(this.$route.query.id)
  },
  computed: {
  },
  watch: {
    $route: {
      handler (val, old) {
        console.log(val)
        this.getGoodDetail(val.query.id)
      }
    }
  },
  mounted () {
  },
  methods: {
    submitComment () {
      this.$http({
        url: this.$http.adornUrl('/commodity/commodity/comment'),
        method: 'post',
        data: this.$http.adornData({
          id: this.goodObj.id,
          content: this.form.evaluation,
          star: this.form.star
        }, false)
      }).then(res => {
        console.log(res)
        if (res.data.code === 0) {
          console.log('111')
          this.dialogFormVisible = false
        }
      })
    },
    collectGood () {
      this.$http({
        url: this.$http.adornUrl('/commodity/commodity/collection'),
        method: 'post',
        data: this.$http.adornData({
          id: this.goodObj.id
        })
      }).then(res => {
        console.log(res)
        if (res.data.code === 0) {
          this.goodObj.collect = true
        }
      })
    },
    getGoodDetail (id) {
      console.log(id)
      if (id === undefined) return
      this.$http({
        url: this.$http.adornUrl(`/commodity/commodity/info/${id}`),
        method: 'get'
      }).then(({data}) => {
        this.goodObj = data.commodity
        console.log(this.goodObj)
        // this.ImgArr = this.goodObj.imgUrl
      })
    },
    likeIt (item) {
      item.like = !item.like
      if (item.like === true) {
        item.num++
      } else {
        item.num--
      }
    },
    goQQ () {
      window.open('https://www.qq.com', '_blank')
    },
    goWX () {
      window.open('https://www.weixin.com', '_blank')
    },
    goWB () {
      window.open('https://www.weibo.com', '_blank')
    },
    handleClick(tab, event) {
      console.log(tab, event)
    }
  },
  destroyed () {
    console.log('销毁1')
  },
  deactivated () {
    console.log('销毁2')
    this.goodObj.commName = ''
    this.goodObj.imgUrl = []
    this.ImgArr = [
      'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fzkres2.myzaker.com%2F202004%2F5ea295f4622768f61400027b_1024.jpg&refer=http%3A%2F%2Fzkres2.myzaker.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1614627895&t=2054eff631947a222ab8f845cd7bc770'
    ]
  }
}
</script>

<style lang="scss" scoped>
.menu-wrap {
  .comment {
    display: flex;
    justify-content: flex-start;
    align-items: center;
    height: 110px;
    border-bottom: 1px solid #898989;
    img {
      border-radius: 100%;
      width: 50px;
      height: 50px;
    }
  }
}
.goods-name {
  width: 200px;
  overflow: hidden;
  white-space: nowrap;
  display: inline-block;
  text-overflow: ellipsis;
  -o-text-overflow: ellipsis;
  font-size: 14px;
  color: #8a979e;
}
.tool-box {
  display: flex;
  justify-content: space-around;
  width: 1000px;
  margin-left: -50px;
  margin-bottom: 30px;

  .top {
    text-align: center;
  }

  .left {
    float: left;
    width: 60px;
  }

  .right {
    float: right;
    width: 60px;
  }

  .bottom {
    clear: both;
    text-align: center;
  }

  .item {
    margin: 4px;
  }

  .left .el-tooltip__popper,
  .right .el-tooltip__popper {
    padding: 8px 10px;
  }
}

.vLine {
  border-left: solid 2px #ccc;
  height: 20px;
  vertical-align: middle;
  display: inline-block;
  margin: 0 30px 0 30px;
}

.color-8a {
  font-size: 14px;
  margin: 3px 0 3px 0;
  color: #8a979e;

  &.share {
    i {
      margin-left: 10px;
      font-size: 20px;
      cursor: pointer;
    }
  }
}

.active {
  color: #409eff;
  background: #ecf5ff;
  border-color: #b3d8ff;
}

.main-footer {
  width: 100%;
  margin: 30px auto;
  min-height: 300px;
  padding: 10px 10px 10px 30px;
  border: 1px solid #f1f4f5;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04);

  .title {
    margin-top: 5px;
    margin-bottom: 30px;
    color: #8a979e;
  }

  .content {
    margin: 10px 0 10px 0;
    color: #8a979e;

    .box {
      height: 50px;
      line-height: 50px;
      margin-left: -50px;
      width: 1000px;
      display: flex;
      justify-content: space-around;
      align-items: center;
    }

    span {
      width: 220px;
      overflow: hidden;
      white-space: nowrap;
      text-overflow: ellipsis;
      -o-text-overflow: ellipsis;
    }
  }
}

.main {
  width: 1200px;
  margin: 0 auto;
  height: 1000px;
  border: 1px solid #f1f4f5;
  border-radius: 4px;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04);

  .about {
    //width: 400px;
    flex: 0 1 400px;
    min-height: 750px;
    padding: 10px 0 20px 0;
    margin: 20px 20px 0 50px;
    //border: 1px solid #f1f4f5;
    //box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04);
    .el-tag--medium {
      margin: 10px 5px 0 5px;
      border-radius: 20px;
      min-width: 60px;
      cursor: pointer;

      &:hover {
        transform: scale(2);
      }
    }

    .text {
      font-size: 14px;
    }

    .item {
      margin-bottom: 18px;
    }

    .clearfix:before,
    .clearfix:after {
      display: table;
      content: "";
    }

    .clearfix:after {
      clear: both
    }

    buttom-area {
      margin-top: 30px;
    }

    .box-card {
      margin: 0 auto 20px;
      position: relative;
      width: 380px;

      .head {
        img {
          width: 40px;
          height: 40px;
          margin-right: 5px;
          border-radius: 100%;
          vertical-align: middle;
        }

        .good-name {
          display: inline-block;
          margin-left: 10px;
          color: #8a979e;
        }
      }

      .footer {
        width: 100%;
        margin-top: 20px;
        border-top: 1px solid ghostwhite;
        height: 25px;
        bottom: 0;
        display: flex;
        justify-content: space-around;

        .foot-content {
          text-align: center;

          &.border {
            flex: 1 0 50px;
            border-left: 1px solid ghostwhite;
            border-right: 1px solid ghostwhite;
          }

          &.left {
            flex: 1 0 50px;
          }

          &.right {
            flex: 1 0 50px;
          }
        }
      }
    }

    .box {
      position: relative;
      height: 100px;
      padding: 5px 10px 5px 10px;
      margin: 3px 0 5px 0;

      .box-img {
        height: 80px;
        width: 80px;
      }

      .box-content {
        width: 200px;
        display: inline-block;
        position: relative;
        top: 20px;
        left: 5px;

        .title {
          color: #8a979e;
        }

        .like {
          font-size: 16px;
          margin-top: 20px;
          cursor: pointer;
        }

        .liked {
          color: red;
        }
      }
    }
  }

  .img-area {
    flex: 0 1 650px;
    //width: 650px;
    min-height: 750px;
    padding: 30px 0 0 0;
    margin: 20px 20px 0 50px;
    border: 1px solid #f1f4f5;
    box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04);

    .imgList {
      width: 450px;
      height: 100px;
      margin-top: 20px;
      display: flex;
      overflow-x: auto;
      overflow-y: hidden;
    }

    .imgListShow {
      flex-shrink: 0;
      width: 100px;
      height: 100%;
      float: left;
      margin-right: 10px;
      border: 2px solid #ccc;
      box-sizing: border-box;
    }

    .imgListShow.active {
      border: 2px solid #1989FA;
    }

    .imgListShow img {
      display: block;
      width: 100%;
      height: 100%;
    }

    .imgs {
      position: relative;
      width: 450px;
      height: 450px;
    }

    .imgsList {
      width: 100%;
      height: 100%;
    }

    .imgsList img {
      display: block;
      width: 100%;
      height: 100%;
    }

    .gallery-thumbs {
      height: 20% !important;
      box-sizing: border-box;
      padding: 10px 0;
      width: 450px;
    }

    .gallery-thumbs .swiper-slide {
      width: 12%;
      height: 100%;
      opacity: 0.4;
    }

    .gallery-thumbs .swiper-slide img {
      display: block;
      width: 50px;
      height: 50px;
    }

    .gallery-thumbs .swiper-slide-active {
      opacity: 1;
    }

  }
}
</style>
