<template>
  <div id="app" >
    <van-nav-bar title="购物车" right-text="管理" @click-right="onClickRight"/>
    <van-swipe-cell :right-width="65"  v-bind="loading" v-for="(item, index) in shopdata" :key="index" >
      <label :for="item.id"  class="carts-checked ml-8">
        <!-- <input type="checkbox"  v-model="i.isChecked" :checked="i.isChecked" :id="i.item.id" @click="seleceOne(key)">-->
        <van-checkbox v-model="shopdata[index].isChecked" :id="item.id" @click="changeChoose(index)" />
      </label>
      <van-cell-group>
        <van-card class="shoppingCard"
          :thumb="item.productImg"
          :price="item.productPrice "
          :title="item.productName"
          :desc="item.productSize"
        >
        </van-card>
        <van-stepper v-model="item.buyCount" class="totalChange" :id="item.productId" @change="countChange(item.productId, item.buyCount)" />
      </van-cell-group>
      <span class="shopDelate" slot="right" @click="deleteCartItem(item.productId)">删除</span>
    </van-swipe-cell>
    <van-submit-bar
      :price="chooseSum"
      button-text="提交订单"
      @submit="onSubmit"
    >
      <van-checkbox v-model="checked" @click="chooseAll()">全选</van-checkbox>
    </van-submit-bar>
  </div>

</template>
<script>
import { fail } from 'assert';

export default {
  created: function() {
    this.getSCartItems();
  },
  data () {
    return {
      checked: false,
      loading: false,
      chooseSum: 0,
      shopdata: [
      ]
    }
  },
  methods: {
    onClickRight () {

    },
    getSCartItems () {
      this.$http({
        method: 'get',
        url: '/shopping-cart/getList',
        headers: {
          'Content-Type' : 'application/x-www-form-urlencoded'
        },
        params: {
          userId: "1"
        }
      }).then((response) => {
        var data = response.data.data;
        this.shopdata = data;
        for (var x = 0; x < this.shopdata.length; x++) {
          this.shopdata[x].isChecked = false;
        }
        this.calculateSum();
      });
    },
    countChange (productId, count) {
      this.$http({
        method: 'post',
        url: '/shopping-cart/changeCart',
        headers: {
          'Content-Type' : 'application/x-www-form-urlencoded'
        },
        params: {
          "userId" : "1",
          "productId": productId,
          "count": count,
          "way": "1"
        }
      }).then((response) => {
        var data = response.data;
        if (data.code != "200") {
          alert(data.data);
        }
        this.calculateSum();
      });
    },
    deleteCartItem (productId) {
      this.$http({
        method: 'post',
        url: '/shopping-cart/deleteCartList',
        headers: {
          'Content-Type' : 'application/x-www-form-urlencoded'
        },
        params: {
          "userId" : "1",
          "productId": productId
        }
      }).then((response) => {
        var data = response.data;
        alert(data.data);
        this.getSCartItems();
        this.calculateSum();
        this.$parent.getShopCartSum();
        // if (data.code != "200") {
        //   alert(data.data);
        // }
      });
    },
    onSubmit () {
      var idList = "";
      var c = 0;
      for (var i = 0; i < this.shopdata.length; i++) {
        if(this.shopdata[i].isChecked == true) {
          idList = idList + this.shopdata[i].productId +",";
          c++;
        }
      }
      if (c < 1) {
        alert("请先选择要购买的产品哟~");
      } else {
        this.$router.push({
          path:'/checkOrder',
          query: {
            productId: idList
          }
        });
      }

    },
    chooseAll () {
      for (var i = 0; i < this.shopdata.length; i++) {
        this.shopdata[i].isChecked = this.checked;
      }
      this.calculateSum();
    },
    // 计算合计总额
    calculateSum () {
      var sum = 0;
      for (var i = 0; i < this.shopdata.length; i++) {
        if (this.shopdata[i].isChecked == true) {
          sum += this.shopdata[i].productPrice * 100 * this.shopdata[i].buyCount;
        }
      }
      this.chooseSum = sum;
    },
    changeChoose (x) {
      if (this.shopdata[x].isChecked == false) {
        this.checked = false;
      } else {
        var flag = 1;
        for (var i = 0; i < this.shopdata.length; i++) {
          if (this.shopdata[i].isChecked == false) {
            flag = 0;
          }
        }
        if (flag == 1) {
          this.checked = true;
        }

      }
      this.calculateSum();
    }
  }
}
</script>

<style lang="less" scoped>
.shoppingCard {
  width: 100%;
  height: 65px;
  font-size: 17px;
}
.van-cell-group {
  float: right;
  width: 90%;
  height: 100px;
  border-bottom:2px solid rgb(187, 187, 187);
}
.van-checkbox {
  float: left;
  margin-left: 3%;
  margin-top: 36px;
}
.totalChange {
  float: right;
}
.shopDelate {
  color: red;
  line-height: 100px;
  text-align: center;
  margin-left: 10px;
}
.van-submit-bar {
  margin-bottom: 50px;
}
.van-checkbox {
  margin-bottom: 32px;
}
</style>
