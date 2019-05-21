<template>
  <div id="order">
    <van-nav-bar
      title="确认订单"
      left-arrow
      @click-left="onClickLeft"
    />
    <div class="choose-address"  @click="changeAddr()">
      <van-icon class="icon" name="location-o" size="40px" />
      <div class="address">
        <span class="address-desc">
          <div class="address-name">{{chosenName}}  {{chosenTel}}</div>
          <div class="address-desc">{{chosenAddress}}</div>
        </span>
        <van-icon class="changeAddr" name="arrow" size="30px"/>
      </div>
    </div>
    <div class="checkList" >
      <van-swipe-cell :right-width="65" v-for="(item, index) in shopdata" :key="index" >
        <van-cell-group>
          <van-card class="checkCard"
            :thumb="item.productImg"
            :price="item.productPrice "
            :title="item.productName"
            :desc="item.productSize"
            :num="item.buyCount"
          >
          </van-card>
        </van-cell-group>
        <span class="shopDelate" slot="right" @click="deleteCartItem(item.productId)">删除</span>
      </van-swipe-cell>
    </div>

    <van-submit-bar
      :price="chooseSum"
      button-text="确认付款"
      @submit="onSubmit"
      class="payMoney"
    >
    </van-submit-bar>
  </div>
</template>
<script>
export default {
  created: function() {
    this.getOrderList();
    this.getDefaultAddr();
  },
  data () {
    return {
      shopdata :[],
      chooseSum: 0,
      chosenAddressId: '',
      chosenName: '姓名',
      chosenTel: '电话',
      chosenAddress: '收货地址( 请选择您的收获地址 )'
    }
  },
  methods: {
    onClickLeft () {
      this.$router.push({
        path:'/shoppingCart',
      })
    },
    getOrderList () {
      var pId = this.$route.query.productId;
      this.$http({
        method: 'get',
        url: '/shopping-cart/checkOrder',
        headers: {
          'Content-Type' : 'application/x-www-form-urlencoded'
        },
        params: {
          userId: "1",
          productId: pId
        }
      }).then((response) => {
        var data = response.data.data;
        this.shopdata = data.cartList;
        var sum = data.sum*100;
        this.chooseSum = sum;

      });
    },
    onSubmit () {

    },
    onAdd() {

    },
    getDefaultAddr () {
      this.$http({
        method: 'get',
        url: '/address/getUserDefaltAddress',
        headers: {
          'Content-Type' : 'application/x-www-form-urlencoded'
        },
        params: {
          userId: "1"
        }
      }).then((response) => {
        if (response.data != null) {
          var info = response.data.data;
          this.chosenAddressId = info.id;
          this.chosenName = info.name;
          this.chosenTel = info.phone;
          var addr = "";
          if (info.province != null) {
            addr += info.province;
          }
          if (info.city != null) {
            addr += info.city;
          }
          if (info.county != null) {
            addr += info.county;
          }
          if (info.addrDesc != null) {
            addr += info.addrDesc;
          }
          if (addr != null) {
            this.chosenAddress = addr;
          }
        }


      });
    },
    changeAddr () {
      this.$router.push({
        path:'/toChooseAddress',
        query: {
          userId: "1"
        }
      });
    }
  }
}
</script>

<style lang="less" scoped>
#order {
  background-color: #E5E5E5;
  height: 100%;
}
.checkCard {
  width: 100%;
  height: 100px;
  font-size: 18px;

}
.van-card__content {
  height: 100px;
}
.van-card__title {
  height: 25px;
}

.van-cell-group {
  width: 100%;
  height: 100px;
}
.choose-address {
  width: 94%;
  height: 100px;
  margin-top: 15px;
  background-color: #fff;
  border-radius: 10px;
  margin-left: 3%;
  margin-right: 3%;
  margin-bottom: 15px;
}
.checkList {
  padding-top: 10px;
  padding-bottom: 10px;
  width: 94%;
  background-color: #fff;
  border-radius: 10px;
  margin-left: 3%;
  margin-right: 3%;
  margin-bottom: 10px;
}
.choose-address .icon {
  width: 10%;
  float: left;
  margin-top: 30px;
  padding-left: 10px;
}
.choose-address .address {
  width: 85%;
  float: right;
  margin-top: 5px;
}
.choose-address .address .address-desc {
  width: 90%;
  float: left;
  line-height: 30px;
}
.choose-address .address .changeAddr {
  width: 10%;
  float: right;
  margin-top: 30px;
}
</style>
