<template>
  <view>
    <view class="search-box">
      <my-search @click="gotoSearch"></my-search>
    </view>
    <swiper
      class="swiper"
      :indicator-dots="true"
      :autoplay="true"
      :interval="3000"
      :duration="1000"
      :circular="true"
    >
      <swiper-item v-for="(item, i) in swiperList" :key="i">
        <view class="swiper-item">
          <navigator
            class="swiper-item"
            :url="
              '/subpackage/goods_detail/goods_detail?goods_id=' + item.goods_id
            "
          >
            <img :src="item.image_src" alt="图片" />
          </navigator>
        </view>
      </swiper-item>
    </swiper>
    <view class="nav-list">
        <view class="nav-item" v-for="(item, i) in navList" :key="i" @click="navClickhandler(item)">
            <image :src="item.image_src" class="nav-img"></image>
        </view>
    </view>
    <view class="floor-list">
    <!-- 楼层 item 项 -->
        <view class="floor-item" v-for="(item, i) in floorList" :key="i">
            <!-- 楼层标题 -->
            <image :src="item.floor_title.image_src" class="floor-title"></image>
            <view class="floor-img-box">
            <!-- 左侧大图片的盒子 -->
                <navigator class="left-img-box" :url="item.product_list[0].url">
                    <image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
                </navigator>
                <!-- 右侧 4 个小图片的盒子 -->
                <view class="right-img-box">
                    <navigator class="right-img-item" v-for="(item2, i2) in item.product_list" :key="i2" v-if="i2 !== 0" :url="item2.url">
                        <image :src="item2.image_src" mode="widthFix" :style="{width: item2.image_width + 'rpx'}"></image>
                    </navigator>
                </view>
            </view>
        </view>
    </view>
  </view>
</template>
<script>
import MySearch from '../../components/my-search/my-search.vue';
export default {
  components: {
    MySearch
  },
  data() {
    return {
      swiperList: [],
      navList: [],
      floorList: []
    };
  },
  onLoad() {
    this.getSwippers();
    this.getNavList();
    this.getFloorList();
  },
  methods: {
    async getSwippers() {
      const { data: res } = await uni.$http.get("/api/public/v1/home/swiperdata");
      console.log(res);
      if (res.meta.status !== 200) {
        return uni.$showMsg();
      }
      this.swiperList = res.message;
    },
    async getNavList() {
        const {data:res} = await uni.$http.get("/api/public/v1/home/catitems");
        if(res.meta.status !== 200) {
            return uni.$showMsg();
        }
        this.navList = res.message;
    },
    async getFloorList() {
        const { data: res } = await uni.$http.get('/api/public/v1/home/floordata')
        if (res.meta.status !== 200) return uni.$showMsg()
        res.message.forEach(ele => {
            ele.product_list.forEach(prod => {
                prod.url = "/subpackage/goods_list/goods_list?" + prod.navigator_url.split("?")[1];
            })
        });
        this.floorList = res.message;
    },
    navClickhandler(item) {
        if(item.name == "分类") {
            uni.switchTab({
                url: '/pages/cate/cate'
            })
        }
    },
    gotoSearch() {
      uni.navigateTo({
          url: "/subpackage/search/search"
      })
    }
  },
};
</script>

<style lang="scss">
swiper {
  height: 330rpx;
  .swiper-item,
  img {
    width: 100%;
    height: 100%;
  }
}
.nav-list {
    display: flex;
    justify-content: space-around;
    margin: 15px 0;
    .nav-img {
        width: 128rpx;
        height: 128rpx;
    }
}
.floor-title {
    width: 100%;
    height: 60rpx;
}
.floor-img-box {
    display: flex;
    padding-left: 10rpx;
}
.right-img-box {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
}
.search-box {
  position: sticky;
  top: 0;
  z-index: 999;
}
</style>