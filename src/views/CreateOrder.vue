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
  <div class="create-order">
    <s-header :name="'生成订单'" @callback="deleteLocal"></s-header>
    <div class="address-wrap">
      <div class="name" @click="goTo">
        <span>{{ address.userName }} </span>
        <span>{{ address.userPhone }}</span>
      </div>
      <div class="address">
        {{ address.provinceName }} {{ address.cityName }} {{ address.regionName }} {{ address.detailAddress }}
      </div>
      <van-icon class="arrow" name="arrow" />
    </div>
    <div class="good">
      <div class="good-item" v-for="(item, index) in cartList" :key="index">
        <div class="good-img"><img :src="prefix(item.goodsCoverImg)" alt=""></div>
        <div class="good-desc">
          <div class="good-title">
            <span>{{ item.goodsName }}</span>
            <span>x{{ item.goodsCount }}</span>
          </div>
          <div class="good-btn">
            <div class="price">¥{{ item.sellingPrice }}</div>
          </div>
        </div>
      </div>
    </div>
    <div class="pay-wrap">
      <div class="price">
        <span>商品金额</span>
        <span>¥{{ total }}</span>
      </div>
      <van-button @click="createOrder" class="pay-btn" color="#1baeae" type="primary" block>生成订单</van-button>
    </div>
    <van-popup
      closeable
      :close-on-click-overlay="false"
      v-model="showPay"
      position="bottom"
      :style="{ height: '25%' }"
      @close="close"
    >
      <div :style="{ width: '90%', margin: '0 auto', padding: '50px 0' }">
        <div>
          <van-button class="alipayBtnBox" color="#1989fa" block @click="payOrder()">支付宝支付</van-button>
        </div>
      </div>
    </van-popup>
  </div>
</template>

<script>
import sHeader from '@/components/SimpleHeader'
import { getByCartItemIds } from '../service/cart'
import { getDefaultAddress, getAddressDetail } from '../service/address'
import { createOrder, payOrder } from '../service/order'
import { setLocal, getLocal } from '@/common/js/utils'
import { Toast } from 'vant'

export default {
  components: {
    sHeader
  },
  data() {
    return {
      cartList: [],
      address: {},
      showPay: false,
      orderNo: '',
      cartItemIds: []
    }
  },
  mounted() {
    this.init()
  },
  methods: {
    async init() {
      Toast.loading({ message: '加载中...', forbidClick: true });
      const { addressId, cartItemIds } = this.$route.query
      const _cartItemIds = cartItemIds ? JSON.parse(cartItemIds) : JSON.parse(getLocal('cartItemIds'))
      setLocal('cartItemIds', JSON.stringify(_cartItemIds))
      const { data: list } = await getByCartItemIds({ cartItemIds: _cartItemIds.join(',') })
      const { data: address } = addressId ? await getAddressDetail(addressId) : await getDefaultAddress()
      if (!address) {
        this.$router.push({ path: 'address' })
        return
      }
      this.cartList = list
      this.address = address
      Toast.clear()
    },
    goTo() {
      this.$router.push({ path: `address?cartItemIds=${JSON.stringify(this.cartItemIds)}` })
    },
    deleteLocal() {
      setLocal('cartItemIds', '')
    },
    async createOrder() {
      const params = {
        addressId: this.address.addressId,
        cartItemIds: this.cartList.map(item => item.cartItemId)
      }
      const { data, resultCode } = await createOrder(params)
      setLocal('cartItemIds', '')
      this.orderNo = data
      this.showPay = true
    },
    close() {
      this.$router.push({ path: 'order' })
    },
    async payOrder(type) {
      Toast.loading()
      const {data}=await payOrder(this.orderNo)
      // this.$router.push({ path: 'order' })
      document.write(data);
    }
  },
  computed: {
    total: function() {
      let sum = 0
      this.cartList.forEach(item => {
        sum += item.goodsCount * item.sellingPrice
      })
      return sum
    }
  }
}
</script>

<style lang="less" scoped>
  @import '../common/style/mixin';
  .create-order {
    background: #f9f9f9;
    .address-wrap {
      margin-bottom: 20px;
      background: #fff;
      position: relative;
      margin-top: 44px;
      font-size: 14px;
      padding: 15px;
      color: #222333;
      .name, .address {
        margin: 10px 0;
      }
      .arrow {
        position: absolute;
        right: 10px;
        top: 50%;
        transform: translateY(-50%);
        font-size: 20px;
      }
      &::before {
        position: absolute;
        right: 0;
        bottom: 0;
        left: 0;
        height: 2px;
        background: -webkit-repeating-linear-gradient(135deg, #ff6c6c 0, #ff6c6c 20%, transparent 0, transparent 25%, #1989fa 0, #1989fa 45%, transparent 0, transparent 50%);
        background: repeating-linear-gradient(-45deg, #ff6c6c 0, #ff6c6c 20%, transparent 0, transparent 25%, #1989fa 0, #1989fa 45%, transparent 0, transparent 50%);
        background-size: 80px;
        content: '';
      }
    }
    .good {
      margin-bottom: 120px;
    }
    .good-item {
      padding: 10px;
      background: #fff;
      display: flex;
      .good-img {
        img {
          .wh(100px, 100px)
        }
      }
      .good-desc {
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        flex: 1;
        padding: 20px;
        .good-title {
          display: flex;
          justify-content: space-between;
        }
        .good-btn {
          display: flex;
          justify-content: space-between;
          .price {
            font-size: 16px;
            color: red;
            line-height: 28px;
          }
          .van-icon-delete {
            font-size: 20px;
            margin-top: 4px;
          }
        }
      }
    }
    .pay-wrap {
      position: fixed;
      bottom: 0;
      left: 0;
      width: 100%;
      background: #fff;
      padding: 10px 0;
      padding-bottom: 50px;
      border-top: 1px solid #e9e9e9;
      >div {
        display: flex;
        justify-content: space-between;
        padding: 0 5%;
        margin: 10px 0;
        font-size: 14px;
        span:nth-child(2) {
          color: red;
          font-size: 18px;
        }
      }
      .pay-btn {
        position: fixed;
        bottom: 7px;
        right: 0;
        left: 0;
        width: 90%;
        margin: 0 auto;
      }
    }
    .alipayBtnBox{
      width: 80%;
      height: 30%;
      position: absolute;
      left: 50%;
      top: 50%;
      transform: translate(-50%,-50%);
    }
  }
</style>
