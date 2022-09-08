<template>
  <view class="goods-detail-container">
    <!-- 轮播图区域 -->
    <swiper indicator-dots circular>
      <swiper-item v-for="(item, i) in goods_info.pics" :key="i">
        <image @click="previewImg(i)" :src="item.pics_sma_url"></image>
      </swiper-item>
    </swiper>

    <!-- 商品信息区域 -->
    <view class="goods-info-box">
      <!-- 商品价格 -->
      <view class="price">￥{{goods_info.goods_price | toFixed}}</view>
      <!-- 信息主体区域 -->
      <view class="goods-info-body">
        <!-- 商品名称 -->
        <view class="goods-name">{{goods_info.goods_name}}</view>
        <!-- 收藏 -->
        <view class="favi">
          <uni-icons type="star" size="18" color="gray"></uni-icons>
          <text>收藏</text>
        </view>
      </view>
      <!-- 运费 -->
      <view class="yf">快递：免运费</view>
    </view>

    <!-- 商品详情信息 -->
    <rich-text :nodes="goods_info.goods_introduce"></rich-text>


    <!-- 商品导航组件 -->
    <van-goods-action>
      <van-goods-action-icon open-type="contact" icon="chat-o" text="客服" dot />
      <van-goods-action-icon url="/pages/cart/cart" link-type="switchTab" icon="cart-o" text="购物车" info="5" />
      <van-goods-action-icon icon="shop-o" text="店铺" />
      <van-goods-action-button text="加入购物车" type="warning" />
      <van-goods-action-button text="立即购买" />
    </van-goods-action>

  </view>
</template>

<script>
  import {
    getGoodsDetail
  } from '@/api/goods.js'
  export default {
    data() {
      return {
        goods_info: {},
      };
    },
    methods: {
      async loadGoodsDetail(id) {
        const res = await getGoodsDetail(id)

        res.goods_introduce = res.goods_introduce.replace(/<img /g, '<img style="display:block;" ')
        res.goods_introduce = res.goods_introduce.replace(/webp/g, 'jpg')
        // console.log(res)
        this.goods_info = res
      },

      // 实现轮播图的预览效果
      previewImg(i) {
        // 调用 uni.previewImage() 方法预览图片
        const urls = this.goods_info.pics.map(item => item.pics_big_url)
        uni.previewImage({
          // 预览时，默认显示图片的索引
          current: i,
          loop: true,
          // 所有图片 url 地址的数组
          urls
        })
      }

    },
    onLoad(options) {
      // 调用请求商品详情数据的方法
      this.loadGoodsDetail(options.id)
    }

  }
</script>

<style lang="scss">
  swiper {
    height: 750rpx;

    image {
      width: 100%;
      height: 100%;
    }
  }

  // 商品信息区域的样式
  .goods-info-box {
    padding: 10px;
    padding-right: 0;

    .price {
      color: #c00000;
      font-size: 18px;
      margin: 10px 0;
    }

    .goods-info-body {
      display: flex;
      justify-content: space-between;

      .goods-name {
        font-size: 13px;
        padding-right: 10px;
      }

      // 收藏区域
      .favi {
        width: 120px;
        font-size: 12px;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        border-left: 1px solid #efefef;
        color: gray;
      }
    }

    // 运费
    .yf {
      margin: 10px 0;
      font-size: 12px;
      color: gray;
    }
  }

  .goods-detail-container {
    // 给页面外层的容器，添加 50px 的内padding，
    // 防止页面内容被底部的商品导航组件遮盖
    padding-bottom: 50px;
  }
</style>
