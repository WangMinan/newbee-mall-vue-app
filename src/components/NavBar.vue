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
  <div class="nav-bar">
    <ul class="nav-list">
      <router-link tag="li" class="nav-list-item active" to="home">
        <em class="nbicon nblvsefenkaicankaoxianban-1"></em>
        <span>首页</span>
      </router-link>
      <router-link tag="li" class="nav-list-item" to="category">
        <em class="nbicon nbfenlei"></em>
        <span>分类</span>
      </router-link>
      <router-link tag="li" class="nav-list-item" to="cart">
        <van-icon  name="shopping-cart-o" :info="!count ? '' : count" />
        <span>购物车</span>
      </router-link>
      <router-link tag="li" class="nav-list-item" to="user">
        <em class="nbicon nblvsefenkaicankaoxianban-"></em>
        <span>我的</span>
      </router-link>
    </ul>
  </div>
</template>

<script>
  import { getLocal } from '../common/js/utils'
  export default {
    mounted() {
      const token = getLocal('token')
      const path = this.$route.path
      if (token && path != '/home') {
        this.$store.dispatch('updateCart')
      }
    },
    data() {
      return {}
    },
    computed: {
      count () {
        return this.$store.state.cartCount
      }
    }
  }
</script>

<style lang="less" scoped >
    @import '../common/style/mixin';
    .nav-bar{
      position: fixed;
      left: 0;
      bottom: 0;
      width: 100%;
      padding: 5px 0;
      z-index: 1000;
      background: #fff;
      transform: translateZ(0);
      -webkit-transform: translateZ(0);
      .nav-list {
        width: 100%;
        .fj();
        flex-direction: row;
        padding: 0;
        .nav-list-item {
          display: flex;
          flex: 1;
          flex-direction: column;
          text-align: center;
          color: #666;
          &.router-link-active {
            color: @primary;
          }
          i {
            text-align: center;
            font-size: 22px;
          }
          span{
            font-size: 12px;
          }
          .van-icon-shopping-cart-o {
            margin: 0 auto;
            margin-bottom: 2px;
          }
        }
      }
    }
</style>
