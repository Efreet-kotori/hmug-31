<template>
  <view>
    <view>
      <van-card v-for="item in goods" :key="item.goods_id" :thumb="item.goods_small_logo ||defaultPic"
        :price="item.goods_price | toFixed " :title="item.goods_name"
        :thumb-link="`/subpkg/goods_detail/goods_detail?id=${item.goods_id}`" />
    </view>
  </view>
</template>

<script>
  import {
    getGoodsList
  } from '@/api/goods.js'
  import toast from '@/utils/toast.js'
  export default {
    data() {
      return {
        queryData: {
          query: '', //关键字
          cid: '', //分类id
          pagenum: 1, //页码
          pagesize: 10 //每页条数
        },
        goods: [],
        total: 0,
        // 是否正在请求数据
        isloading: false,
        defaultPic: 'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png'

      }
    },
    methods: {
      async loadGoodsList(stopPullDown) {
        // ** 打开节流阀
        this.isloading = true
        const {
          goods,
          total
        } = await getGoodsList(this.queryData)
        // ** 关闭节流阀
        this.isloading = false
        this.goods = [...this.goods, ...goods]
        this.total = total
        stopPullDown && stopPullDown()
      }
    },
    onLoad({
      query
    }) {
      // console.log(query)
      this.queryData.query = query || ''
      this.loadGoodsList()
    },
    // 触底的事件
    onReachBottom() {
      // 判断是否还有下一页数据
      if (this.queryData.pagenum * this.queryData.pagesize >= this.total) return toast('数据加载完毕!')
      // 判断是否正在请求其它数据，如果是，则不发起额外的请求
      if (this.isloading) return
      // 让页码值自增 +1
      this.queryData.pagenum += 1
      // 重新获取列表数据
      this.loadGoodsList()
    },
    // 下拉刷新的事件
    onPullDownRefresh() {
      // 1. 重置关键数据
      // this.queryData.pagenum = 1
      this.queryData = {
        query: this.queryData.query, //关键字
        cid: '', //分类id
        pagenum: 1, //页码
        pagesize: 10 //每页条数
      }
      this.isloading = false
      this.total = 0
      this.goods = []
      // 2. 重新发起请求
      this.loadGoodsList(() => uni.stopPullDownRefresh())
    }

  }
</script>

<style>

</style>
