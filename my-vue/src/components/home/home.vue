<template>
  <div id="home">
    <van-search v-model="value" placeholder="请输入搜索关键词" show-action shape="round" @search="onSearch">
      <div slot="action" @click="onSearch">搜索</div>
    </van-search>
    <van-swipe :autoplay="3000" indicator-color="white">
      <van-swipe-item v-for="(image, index) in images" :key="index">
        <img :src="image" height="230px">
      </van-swipe-item>
    </van-swipe>
    <ul class="goodsList">
      <li
        @load="getProductItems()"
        v-bind="loading"
        v-for="(item, index) in goodsList"
        :key="index"
      >
        <van-card
          tag="热销"
          :price="item.price"
          :desc="item.productDesc"
          :title="item.productName"
          :thumb="item.productImg"
          :origin-price="item.originPrice"
        >
          <div slot="footer" class="footer" >
            <van-icon name="add-o" size="28px" :id="item.productId" class="addToShopCard" @click="buyProduct(item.productId)"/>
          </div>
        </van-card>
      </li>
    </ul>
    <van-sku
      v-model="skushow"
      :sku="sku"
      :goods="goods"
      :goods-id="skuData.goodsId"
      :hide-stock="sku.hide_stock"
      @buy-clicked="onBuyClicked"
      @add-cart="onAddCartClicked"
      :close-on-click-overlay="true"
    />
  </div>
</template>
<script>
import axios from "axios";
import qs from "qs";
import appVue from '@/App.vue';

export default {
  created: function() {
    this.getProductItems();
  },
  data() {
    return {
      value: "",
      loading: false, // 是否处于加载状态
      skushow: false,
      onSearch: "",
      // nowProductId: "",
      // nowProductName: "",
      // nowProductImg: "",
      // nowProductSize: "",
      images: [
        "./static/homeImg/a1.jpg",
        "./static/homeImg/a2.jpg",
        "./static/homeImg/a3.jpg"
      ],
      goodsList: [
      ],
      sku: {
        // 所有sku规格类目与其值的从属关系，比如商品有颜色和尺码两大类规格，颜色下面又有红色和蓝色两个规格值。
        // 可以理解为一个商品可以有多个规格类目，一个规格类目下可以有多个规格值。
        tree: [
          {
            k: "规格", // skuKeyName：规格类目名称
            v: [
              {
                id: "30349", // skuValueId：规格值 id
                name: "2盒 每盒6个", // skuValueName：规格值名称
                imgUrl: "./static/productImg/mangguogan-logo.png" // 规格类目图片，只有第一个规格类目可以定义图片
              }
            ],
            k_s: "s1" // skuKeyStr：sku 组合列表（下方 list）中当前类目对应的 key 值，value 值会是从属于当前类目的一个规格值 id
          }
        ],
        // 所有 sku 的组合列表，比如红色、M 码为一个 sku 组合，红色、S 码为另一个组合
        list: [
          {
            id: 2259, // skuId，下单时后端需要
            price: 5400, // 价格（单位分）
            s1: "30349", // 规格类目 k_s 为 s1 的对应规格值 id
            s2: "0", // 规格类目 k_s 为 s2 的对应规格值 id
            s3: "0", // 最多包含3个规格值，为0表示不存在该规格
            stock_num: 110 // 当前 sku 组合对应的库存
          }
        ],
        price: "54.00", // 默认价格（单位元）
        stock_num: 227, // 商品总库存
        collection_id: 30349, // 无规格商品 skuId 取 collection_id，否则取所选 sku 组合对应的 id
        none_sku: false, // 是否无规格商品
        hide_stock: false // 是否隐藏剩余库存
      },
      goods: {
        // 商品标题
        title: "蛋黄酥",
        // 默认商品 sku 缩略图
        picture: "./static/productImg/danhuangsu-logo.png"
      },
      skuData: {
        // 商品 id
        goodsId: "946755",
        // 选择的商品数量
        selectedNum: 1,
        // 选择的 sku 组合
        selectedSkuComb: {
          id: 2257,
          price: 100,
          s1: "30349",
          stock_num: 111
        }
      }
    };
  },
  components: {
    appVue
  },
  methods: {
    onBuyClicked(item) {
      // 点击选项时默认不会关闭菜单，可以手动关闭
      this.show = false;
    },
    onAddCartClicked() {},
    getProductItems() {
      this.$http({
        method: 'get',
        url: '/front-product/getList',
        headers: {
          'Content-Type' : 'application/x-www-form-urlencoded'
        },
        params: {

        }
      }).then((response) => {
        var data = response.data.data;
        this.goodsList = data;
      });
    },
    buyProduct (productId) {
      this.$http({
        method: 'post',
        url: '/front-product/getProductById',
        headers: {
          'Content-Type' : 'application/x-www-form-urlencoded'
        },
        params: {
          "id" : productId
        }
      }).then((response) => {
        var data = response.data;
        var p = data.data;
        // alert(this.sku.tree[0].v[0].name);
        this.sku.tree[0].v[0].name = p.productSize;
        this.sku.tree[0].v[0].imgUrl = p.productImg;
        this.goods.title = p.productName;
        this.sku.list[0].stock_num = p.countSum;
        this.skuData.goodsId = p.productId;
        this.skushow = true;
      });
    },

    onAddCartClicked () {
      this.$http({
        method: 'post',
        url: '/shopping-cart/changeCart',
        headers: {
          'Content-Type' : 'application/x-www-form-urlencoded'
        },
        params: {
          "userId" : "1",
          "productId": this.skuData.goodsId,
          "count": this.skuData.selectedNum,
          "way": "2"
        }
      }).then((response) => {
        var data = response.data;
        var p = data.data;
        alert(p);
        this.$parent.getShopCartSum();
        this.skushow = false;
      });
    }
  }
};
</script>

<style>
.pic img {
  width: 80px;
  float: left;
  margin-left: 10px;
  margin-top: 5px;
}
.goodsList li {
  width: 100%;
  height: 130px;
  font-family: "YouYuan";
  border-bottom: 2px solid rgb(187, 187, 187);
}
.goodsList {
  margin-bottom: 60px;
  margin-top: 5px;
}
.van-card {
  font-size: 17px;
}
.footer .van-button {
  font-size: 16px;
}
.footer {
  height: 30px;
}
.van-stepper {
  float: left;
  margin-left: 60%;
}
.addToShopCard {
  float: right;
}
.van-sku-stepper-container .van-sku__stepper-title {
  float: left;
}

.van-sku-stepper-container .van-stepper{
  float: right;
  position: absolute;
}
</style>
