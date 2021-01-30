<template>
  <div class="flex_box">
    <template v-for="(item, index) in list">
      <img-card :key="index" :item="item"></img-card>
    </template>
  </div>
</template>

<script>
import ImgCard from '../components/img_card'

export default {
  name: 'img_page',
  components: {ImgCard},
  data() {
    return {
      list: [],
      scroll: 0,
      cacheScroll: 0,
      currPage: 1,
      loadData: true,
      loading: false,
      dataList: [
        {
          title: '卡卡',
          like: false,
          num: 100,
          content: '111111222',
          name: '11tjrftjtjhsrtjsrtjrtjkrtfj1',
          imgSrc: 'https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=1374152916,1336053607&fm=26&gp=0.jpg'
        },
        {
          title: '卡卡',
          like: true,
          num: 10,
          content: '111111222',
          name: '222',
          imgSrc: 'https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=1374152916,1336053607&fm=26&gp=0.jpg'
        },
        {
          title: '卡卡',
          like: false,
          num: 1003,
          content: '111111222',
          name: '333',
          imgSrc: 'https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=1363085778,1106765377&fm=26&gp=0.jpg'
        },
        {
          title: '卡卡',
          like: true,
          num: 20,
          content: '111111222',
          name: '444',
          imgSrc: 'https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=2766294875,2596829250&fm=26&gp=0.jpg'
        },
        {
          title: '卡卡',
          like: false,
          num: 40,
          content: '111111222',
          name: '555',
          imgSrc: 'https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=2672043729,2874991295&fm=26&gp=0.jpg'
        },
        {
          title: '卡卡',
          like: false,
          num: 103,
          content: '111111222',
          name: '666',
          imgSrc: 'https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=1837239560,1053782308&fm=11&gp=0.jpg'
        },
        {
          title: '卡卡',
          like: true,
          num: 3,
          content: '111111222',
          name: '777',
          imgSrc: 'https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=1363085778,1106765377&fm=26&gp=0.jpg'
        },
        {
          title: '卡卡',
          like: false,
          num: 2,
          content: '111111222',
          name: '888',
          imgSrc: 'https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=1407430877,1705196138&fm=26&gp=0.jpg'
        },
        {
          title: '卡卡',
          like: true,
          num: 55,
          content: '111111222',
          name: '999',
          imgSrc: 'https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=1374152916,1336053607&fm=26&gp=0.jpg'
        },
        {
          title: '卡卡',
          like: false,
          num: 400,
          content: '111111222',
          name: '100',
          imgSrc: 'https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=1837239560,1053782308&fm=11&gp=0.jpg'
        }
      ]
    }
  },
  mounted () {
    console.log(this.$route.path)
    this.getList()
    window.addEventListener('scroll', this.menu)
  },
  methods: {
    menu () {
      if (!this.loadData) return
      if (this.loading) return
      if (this.$route.path !== '/sys-main') return
      this.scroll = document.documentElement.scrollTop || document.body.scrollTop
      if (this.scroll > this.cacheScroll + 220) {
        this.currPage++
        this.$http({
          url: this.$http.adornUrl('/commodity/commodity/list'),
          method: 'get',
          params: this.$http.adornParams({
            page: this.currPage,
            limit: 10
          })
        }).then(({data}) => {
          this.loading = true
          if (data.page.length === 0) {
            this.loadData = false
            return
          }
          let list = data.page.list
          list.forEach(l => {
            l.like = false
          })
          let newList = this.list.concat(list)
          this.list = newList
          console.log(this.list)
          this.cacheScroll = this.scroll
          this.loading = false
        })
      }
    },
    getList () {
      this.$http({
        url: this.$http.adornUrl('/commodity/commodity/list'),
        method: 'get',
        params: this.$http.adornParams()
      }).then(({data}) => {
        console.log(data)
        this.list = data.page.list
        this.list.forEach(l => {
          l.like = false
        })
      })
    }
  }
}
</script>

<style scoped>
.flex_box {
  display: flex;
  justify-content: center;
  flex-flow: row wrap;
  align-content: flex-start;
  width: 1600px;
  margin: 0 auto;
  box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04);
}
</style>
