<script>
export default {
  name: 'publish',
  data () {
    return {}
  },
  mounted () {
    this.getArticleList()
  },
  methods: {
    getArticleList () {
      this.$http({
        url: this.$http.adornUrl('/sys/articleauthor/list'),
        method: 'get',
        params: this.$http.adornParams({
          'page': this.pageIndex,
          'limit': this.pageSize
        })
      }).then(({data}) => {
        if (data && data.code === 0) {
          console.log(data)
        } else {
          console.log(data)
        }
        this.dataListLoading = false
      })
    }
  },
  render (createElement, context) {
    console.log(context)
    return createElement('h1', 'woaini Zes')
  }
}
</script>

<style scoped>

</style>
