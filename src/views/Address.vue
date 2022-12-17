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
  <div class="address-box">
    <s-header :name="'地址管理'" :back="'/user'"></s-header><!--页面名称为“地址管理”，上级页面为“我的”页面-->
    <div class="address-item">
      <van-address-list
        v-if="from != 'mine'"
        v-model="chosenAddressId"
        :list="list"
        default-tag-text="默认"
        @add="onAdd"
        @edit="onEdit"
        @select="select"
      />
      <van-address-list
        v-else
        v-model="chosenAddressId"
        :list="list"
        default-tag-text="默认"
        @add="onAdd"
        @edit="onEdit"
      />
      <!--v-model 表示选中的地址编号，list 表示要遍历的数据集合,default-tag-text 表示默认地址标签文字，
        @add 表示添加按钮绑定的方法,@edit 表示编辑按钮绑定的方法，@select 表示选中按钮绑定的方法--> 
    </div>
  </div>
</template>

<script>
import sHeader from '@/components/SimpleHeader'//引入SimpleHeader组件，命名为sHeader
import { getAddressList } from '../service/address' //引入address的getAddressList方法
export default {
  components: {
    sHeader
  },
  data() {
    return {
      chosenAddressId: '1',
      list: [],
      from: this.$route.query.from,
    }
  }, //data是一个函数，函数的返回值为地址对象
  async mounted() {
    const { data } = await getAddressList()
    this.list = data.map(item => {
      return {
        id: item.addressId,
        name: item.userName,
        tel: item.userPhone,
        address: `${item.provinceName} ${item.cityName} ${item.regionName} ${item.detailAddress}`,
        isDefault: !!item.defaultFlag
      }
    })
  }, //mounted函数表示组件加载完成后就会执行，显示用户的地址信息
  methods: {
    onAdd() {
      this.$router.push({ path: `address-edit?type=add&from=${this.from}` })//添加地址方法，跳转到添加地址界面
    },
    onEdit(item, index) {
      this.$router.push({ path: `address-edit?type=edit&addressId=${item.id}&from=${this.from}` })//编辑地址方法，跳转到编辑地址界面
    },
    select(item, index) {
      this.$router.push({ path: `create-order?addressId=${item.id}&from=${this.from}` }) //结算时选择地址，跳转到生成订单的界面
    }
  }
}
</script>

<style lang="less">
  @import '../common/style/mixin';
  .address-box {
    .van-radio__icon {
      display: none;
    }
    .address-item {
      margin-top: 44px;
      .van-button {
        background: @primary;
        border-color: @primary;
      }
    }
  }
</style>
