<!--
 * 严肃声明：
 * 开源版本请务必保留此注释头信息，若删除我方将保留所有法律责任追究！
 * 本系统已申请软件著作权，受国家版权局知识产权以及国家计算机软件著作权保护！
 * 可正常分享和学习源码，不得用于违法犯罪活动，违者必究！
 * Copyright (c) 2020 陈尼克 all rights reserved.
 * 版权所有，侵权必究！
 *
-->

<template>
  <div class="product-detail">
    <s-header :name="'商品详情'"></s-header>
    <div class="detail-content">
      <div class="detail-swipe-wrap">
        <van-swipe class="my-swipe" indicator-color="#1baeae">
          <van-swipe-item v-for="(item, index) in detail.goodsCarouselList" :key="index">
            <img :src="prefix(item)" alt="">
          </van-swipe-item>
        </van-swipe>
      </div>
      <div class="product-info">
        <div class="product-title">
          {{ detail.goodsName }}
        </div>
        <div class="product-desc">免邮费 顺丰快递</div>
        <div class="product-price">
          <span>¥{{ detail.sellingPrice }}</span>
          <!-- 库存203 -->
        </div>
      </div>
      <div class="product-intro">
        <ul>
          <li>概述</li>
          <li>参数</li>
          <li>安装服务</li>
          <li>常见问题</li>
        </ul>
        <div class="product-content" v-html="detail.goodsDetailContent"></div>
      </div>
    </div>
    <van-goods-action>
      <van-goods-action-icon icon="chat-o" text="客服" />
      <van-goods-action-icon icon="cart-o" :info="!count ? '' : count" @click="goTo()" text="购物车"/>
      <van-goods-action-button type="warning" @click="addCart" text="加入购物车" />
      <van-goods-action-button type="danger" @click="goToCart" text="立即购买" />
    </van-goods-action>
  </div>
</template>

<script>
import { getDetail } from '@/service/good'//引入good.js的方法
import { addCart } from '@/service/cart' //引入cart.js的方法
import sHeader from '@/components/SimpleHeader' //引入SimpleHeader组件，命名为sHeader
import { Toast } from 'vant'//引入弹窗组件
export default {
  data() {
    return {
      detail: {
        goodsCarouselList: []
      }
    }
  },
  components: {
    sHeader
  },//重用sHeader组件 
  async mounted() {
    const { id } = this.$route.params
    const { data } = await getDetail(id)
    this.detail = data
  },//页面载入时执行的方法，获取商品的信息
  methods: {
    goBack() {
      this.$router.go(-1)
    },//返回上一个页面
    goTo() {
      this.$router.push({ path: '/cart' })
    },//跳转到购物车页面
    async addCart() {
      const { data, resultCode } = await addCart({ goodsCount: 1, goodsId: this.detail.goodsId })
      if (resultCode === 200 ) Toast.success('添加成功')
      this.$store.dispatch('updateCart')
    },//添加商品到购物车
    async goToCart() {
      const { data, resultCode } = await addCart({ goodsCount: 1, goodsId: this.detail.goodsId })
      this.$store.dispatch('updateCart')
      this.$router.push({ path: '/cart' })
    }//立即购买按钮绑定的方法，跳转到购物车页面
  },
  computed: {
    count () {
      return this.$store.state.cartCount
    }
  }//计算属性，返回购物车订单数量
}
</script>

<style lang="less">
  @import '../common/style/mixin';
  .product-detail {
    .detail-header {
      position: fixed;
      top: 0;
      left: 0;
      z-index: 10000;
      .fj();
      .wh(100%, 44px);
      line-height: 44px;
      padding: 0 10px;
      .boxSizing();
      color: #252525;
      background: #fff;
      border-bottom: 1px solid #dcdcdc;
      .product-name {
        font-size: 14px;
      }
    }
    .detail-content {
      margin-top: 44px;
      .detail-swipe-wrap {
        .my-swipe .van-swipe-item {
          img {
            width: 100%;
            // height: 300px;
          }
        }
      }
      .product-info {
        padding: 0 10px;
        .product-title {
          font-size: 18px;
          text-align: left;
          color: #333;
        }
        .product-desc {
          font-size: 14px;
          text-align: left;
          color: #999;
          padding: 5px 0;
        }
        .product-price {
          .fj();
          span:nth-child(1) {
            color: #F63515;
            font-size: 22px;
          }
          span:nth-child(2) {
            color: #999;
            font-size: 16px;
          }
        }
      }
      .product-intro {
        width: 100%;
        ul {
          .fj();
          width: 100%;
          margin: 10px 0;
          li {
            flex: 1;
            padding: 5px 0;
            text-align: center;
            font-size: 15px;
            border-right: 1px solid #999;
            box-sizing: border-box;
            &:last-child {
              border-right: none;
            }
          }
        }
        .product-content {
          padding: 0 20px;
          img {
            width: 100%;
          }
        }
      }
    }
    .van-goods-action-button--warning {
      background: linear-gradient(to right,#6bd8d8, @primary)
    }
    .van-goods-action-button--danger {
      background: linear-gradient(to right, #0dc3c3, #098888)
    }
  }
</style>
