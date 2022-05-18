<template>
    <view class="container" v-if="cart.length !== 0">
        <my-address></my-address>
        <!-- 购物车商品列表的标题区域 -->
        <view class="cart-title">
            <!-- 左侧的图标 -->
            <uni-icons type="shop" size="18"></uni-icons>
            <!-- 描述文本 -->
            <text class="cart-title-text">购物车</text>
        </view>
        <uni-swipe-action>
            <block v-for="(goods, i) in cart" :key="i">
                <!-- uni-swipe-action-item 可以为其子节点提供滑动操作的效果。需要通过 options 属性来指定操作按钮的配置信息 -->
                <uni-swipe-action-item :right-options="options">
                    <my-goods @radio-change="radioChangeHandler" @num-change="numberChangeHandler" :goods="goods" :showradio="true" :shownum="true"></my-goods>
                    <template v-slot:right>
                        <view class="slot-button" @click="swipeActionClickHandler(goods)">
                        <text class="slot-button-text">删除</text>
                        </view>
                    </template>
                </uni-swipe-action-item>
            </block>
        </uni-swipe-action>
        <my-settle></my-settle>
    </view>
    <!-- 空白购物车区域 -->
    <view class="empty-cart" v-else>
        <!-- <image src="../../static/cart_empty.jpeg" class="empty-img"></image> -->
        <text class="tip-text">空空如也~</text>
    </view>
</template>
<script>
import MyGoods from "../../components/my-goods/my-goods.vue";
import MyAddress from "../../components/my-address/my-address.vue"
import MySettle from "../../components/my-settle/my-settle.vue"

import { mapGetters, mapState, mapMutations} from "vuex";
import badgeMix from '../../mixins/tabbar-badge'
export default {
    mixins: [badgeMix],
    components: {
        MyGoods,
        MyAddress,
        MySettle
    },
    data() {
        return {
            options:[{
                text: '删除', // 显示的文本内容
                style: {
                    backgroundColor: '#C00000' // 按钮的背景颜色
                }
            }]
        }
    },
    methods: {
        ...mapMutations("m_cart", ["updateGoodsState", "updateGoodsCount", "removeGoodsById"]),
        radioChangeHandler(e) {
            this.updateGoodsState(e)
        },
        numberChangeHandler(e) {
            this.updateGoodsCount(e)
        },
        swipeActionClickHandler(goods) {
            console.log(goods);
            this.removeGoodsById(goods.goods_id)
        }
    },
    computed: {
        ...mapState("m_cart", ["cart"]),
        ...mapGetters('m_cart', ['total']),
    }
}
</script>

<style lang="scss">
.container {
    padding-bottom: 50px;
}
.cart-title {
    height: 40px;
    display: flex;
    align-items: center;
    font-size: 14px;
    padding-left: 5px;
    border-bottom: 1px solid #efefef;
    .cart-title-text {
        margin-left: 10px;
    }
}
.slot-button {
    background-color: #C00000;
    display: flex;
    width: 150rpx;
    justify-content: center;
    align-items: center;
}
.slot-button-text {  
    color: #f0f0f0;  
    font-size: 14px;  
}
.empty-cart {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 150px;

  .empty-img {
    width: 90px;
    height: 90px;
  }

  .tip-text {
    font-size: 12px;
    color: gray;
    margin-top: 15px;
  }
}
</style>