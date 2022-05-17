<template>
<view>
    <view class="search-box">
    <!-- 使用 uni-ui 提供的搜索组件 -->
        <uni-search-bar @input="input" :radius="100" cancelButton="none" :focus="true"></uni-search-bar>
    </view>  
    <!-- 搜索建议列表 -->
    <view class="sugg-list" v-if="searchResults.length !== 0">
        <view class="sugg-item" v-for="(item, i) in searchResults" :key="i" @click="gotoDetail(item.goods_id)">
            <view class="goods-name">{{item.goods_name}}</view>
            <uni-icons type="arrowright" size="16"></uni-icons>
        </view>
    </view>
    <!-- 搜索历史 -->
    <view class="history-box" v-else>
    <!-- 标题区域 -->
        <view class="history-title">
            <text>搜索历史</text>
            <uni-icons type="trash" size="17" @click="cleanHistory"></uni-icons>
        </view>
        <!-- 列表区域 -->
        <view class="history-list">
            <uni-tag :text="item" v-for="(item, i) in historys" :key="i" @click="gotoGoodsList(item)"></uni-tag>
        </view>
    </view>
</view>
</template>

<script>
export default {
    data() {
        return {
            timer: null,
            keyword: "",
            searchResults: [],
            historyList: ['a', 'app', 'apple']
        }
    },
    onLoad() {
        this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
    },
    methods: {
        input(e) {
            clearTimeout(this.timer);
            // console.log(e);
            this.timer = setTimeout(() => {
                this.keyword = e;
                this.getSearchList();
            }, 500)
        },
        async getSearchList() {
            if (this.keyword === '') {
                this.searchResults = [];
                return;
            }
            // 发起请求，获取搜索建议列表
            // console.log(this.keyword);
            const { data: res } = await uni.$http.get('/api/public/v1/goods/qsearch', { query: this.keyword });
            if (res.meta.status !== 200) return uni.$showMsg();
            this.searchResults = res.message;
            this.saveSearchHistory()
        },
        saveSearchHistory() {
            const set = new Set(this.historyList)
            set.delete(this.keyword)
            set.add(this.keyword)
            this.historyList = Array.from(set)
            // 调用 uni.setStorageSync(key, value) 将搜索历史记录持久化存储到本地
            uni.setStorageSync('kw', JSON.stringify(this.historyList))
        },
        cleanHistory() {
            this.historyList = [],
            uni.setStorageSync('kw', '[]')
        },
        gotoDetail(goods_id) {
            uni.navigateTo({
                // 指定详情页面的 URL 地址，并传递 goods_id 参数
                url: '/subpackage/goods_detail/goods_detail?goods_id=' + goods_id
            })
        },
        gotoGoodsList(kw) {
            uni.navigateTo({
                url: '/subpackage/goods_list/goods_list?query=' + kw
            })
        }
    },
    computed: {
        historys() {
            return [...this.historyList].reverse()
        }
    }
}
</script>
<style lang="scss">
.search-box {
    position: sticky;
    top: 0;
    z-index: 999;
}
.uni-searchbar {
  display: flex;
  flex-direction: row;
  position: relative;
  padding: 16rpx;
  /* 将默认的 #FFFFFF 改为 #C00000 */
  background-color: #c00000;
}
.sugg-list {
    padding: 0 5px;
    .sugg-item {
        display: flex;
        align-items: center;
        justify-content: space-between;
        font-size: 12px;
        padding: 13px 0;
        border-bottom: 1px solid #ececec;
        .goods-name {
            white-space: nowrap;
            text-overflow: ellipsis;
            overflow: hidden;
        }
    }
}
.history-box {
    padding: 0 5px;
    .history-title {
        font-size: 13px;
        display: flex;
        justify-content: space-between;
        height: 40px;
        align-items: center;
        border-bottom: 1px solid #efefef;
    }
    .history-list {
        display: flex;
        flex-wrap: wrap;
        margin-top: 5px;
        .uni-tag {
            margin-top: 5px;
            margin-right: 5px;
            background-color: #ececec;
            border-radius: 2px;
            border: 1px solid #eeeeee;
            color: #333333;
        }
    }
}
</style>