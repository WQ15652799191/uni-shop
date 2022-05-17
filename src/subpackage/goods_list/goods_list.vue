<template>
<view>
    <view class="goods-list">
        <view v-for="(goods, i) in goodsList" :key="i" @click="gotoDetail(goods)">
            <my-goods :goods="goods"></my-goods>
        </view>
    </view>
</view>
</template>
<script>
import MyGoods from "../../components/my-goods/my-goods.vue"
export default {
    components: { MyGoods },

    data() {
        return {
            goods_info: {},
            queryObj: {
                query: '',
                cid: '',
                pagenum: 1,
                pagesize: 10
            },
            total: 0,
            goodsList: [],
            // 是否正在请求数据
            isloading: false
        }
    },
    onLoad(options) {
        console.log(options);
        this.queryObj.query = options.query || "";
        this.queryObj.cid = options.cid || "";
        // 调用请求商品详情数据的方法
        this.getGoodsList()
    },
    methods: {
        async getGoodsList(cb) {
            this.isloading = true;
            // 发起请求
            const { data: res } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
            if (res.meta.status !== 200) return uni.$showMsg()
            // 为数据赋值
            // this.goodsList = res.message.goods
            // 为数据赋值：通过展开运算符的形式，进行新旧数据的拼接
            this.goodsList = [...this.goodsList, ...res.message.goods]
            this.total = res.message.total
            this.isloading = false
            cb && cb();
        },
        gotoDetail(item) {
            uni.navigateTo({
                url: '/subpackage/goods_detail/goods_detail?goods_id=' + item.goods_id
            })
        }
    },
    onReachBottom() {
        // 判断是否还有下一页数据
        if (this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg('数据加载完毕！')

        // 判断是否正在请求其它数据，如果是，则不发起额外的请求
        if(this.isloading) return;

        // 让页码值自增 +1
        this.queryObj.pagenum += 1
        // 重新获取列表数据
        this.getGoodsList()
    },
    onPullDownRefresh() {
        // 1. 重置关键数据
        this.queryObj.pagenum = 1
        this.total = 0
        this.isloading = false
        this.goodsList = []

        // 2. 重新发起请求
        this.getGoodsList(() => uni.stopPullDownRefresh())
    }
}
</script>
