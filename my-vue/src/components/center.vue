<template>
  <div id="app">
    <div class="content">
      <router-view></router-view>
    </div>

    <van-tabbar v-model="active">
      <van-tabbar-item icon="home-o">
        <router-link to="/home">主页</router-link>
      </van-tabbar-item>
      <van-tabbar-item icon="apps-o">
        <router-link to="/categories">分类</router-link>
      </van-tabbar-item>
      <van-tabbar-item icon="shopping-cart-o" :info="shopCartSum">
        <router-link to="/shoppingCart">购物车</router-link>
      </van-tabbar-item>
      <van-tabbar-item icon="manager-o">
        <router-link to="/me">我</router-link>
      </van-tabbar-item>
    </van-tabbar>
  </div>
</template>
<script>
export default {
  name: 'parent',
  created: function() {
    this.getShopCartSum();
  },
  data () {
    return {
      shopCartSum:"0",
      active: 0
    }
  },
  methods: {
    getShopCartSum () {
      this.$http({
        method: 'get',
        url: '/shopping-cart/getShopCartSum',
        headers: {
          'Content-Type' : 'application/x-www-form-urlencoded'
        },
        params: {
          "userId" : "1",
        }
      }).then((response) => {
        var data = response.data;
        var p = data.data;
        this.shopCartSum = p;
      });
    }
  }
}
</script>

<style lang="less">

</style>
