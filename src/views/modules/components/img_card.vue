<template>
<!--  <el-popover-->
<!--    placement="top"-->
<!--    :title="item.title"-->
<!--    width="200"-->
<!--    trigger="hover"-->
<!--    :offset="offsetNum"-->
<!--    :content="item.commName">-->

<!--  </el-popover>-->
  <div slot="reference" class="box">
    <img class="img" @click.stop="goCardDetail(item)" :src="item.imgUrl[0]">
    <div class="content">{{item.brand}}</div>
    <div class="footer">
      <img :src="item.imgUrl[0]" alt="">
      <el-tooltip  effect="light" :content="item.commodityInfo" placement="top">
        <span class="good-name">{{item.commodityName}}</span>
      </el-tooltip>
      <div class="like" :class="{'liked': item.like === true}" @click.stop="likeIt(item)">
        <i :class="[item.like ? yes : no]"></i>
        <span>{{ item.likeCount}}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'img_card',
  data () {
    return {
      offsetNum: 3,
      yes: 'el-icon-star-on',
      no: 'el-icon-star-off'
    }
  },
  props: {
    item: {type: Object,
      default () {
        return {}
      }
    }
  },
  methods: {
    goCardDetail (item) {
      this.$router.push({path: '/sys-store', query: {id: item.id}})
    },
    likeIt (item) {
      this.$http({
        url: this.$http.adornUrl(`/commodity/commodity/like/${item.id}`),
        method: 'post',
        params: this.$http.adornParams()
      }).then(res => {
        console.log(res)
        if (res.data.code === 0) {
          item.like = !item.like
          if (item.like === true) {
            item.likeCount++
          } else {
            item.likeCount--
          }
        }
      })
      // return false
    },
    navigateTo () {
      console.log('111')
    }
  }
}
</script>

<style scoped>
.box {
  cursor: pointer;
  width: 250px;
  height: 400px;
  margin: 10px 20px 10px 20px;
  padding: 10px 5px 5px 10px;
  border: 1px solid #f1f4f5;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04);
}
.box .img {
  border-radius: 4px;
  width: 100%;
  height: 300px;
  overflow: visible;
}
.box .content {
  font-weight:bold;
  margin: 10px auto 10px;
  word-break: normal;
  word-wrap: break-word;
  max-height: 20px;
  text-align: center;
  width: 100px;
}
.box .footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.footer img {
  width: 40px;
  height: 40px;
  margin-right: 5px;
  border-radius: 100%;
  vertical-align: middle;
}
.footer .good-name {
  width: 100px;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
  -o-text-overflow: ellipsis;
  font-size: 14px;
  color: #8a979e;
  margin-left: -50px;
  margin-top: 5px;
}
.like {
  z-index: 9999;
  font-size: 16px;
  margin-top: 20px;
  cursor: pointer;
}
.liked {
  color: red;
}
</style>
