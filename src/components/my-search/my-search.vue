<template>
    <view class="my-search-container" :style="{'background-color': bgcolor}">
        <view class="my-search-box" :style="{'border-radius': radius + 'px'}" @click="searchBoxHandler">
            <uni-icons type="search" size="18"></uni-icons>
            <text class="palceholder">搜索</text>
        </view>
    </view>
</template>
<script>
export default {
    props: {
        bgcolor: {
            type: String,
            default: "#c00000"
        },
        radius: {
            type: Number,
            default: 18
        }
    },
    methods: {
        searchBoxHandler() {
            this.$emit("click");
        }
    }
}
</script>
<style lang="scss">
.my-search-container {
    height: 50px;
    // background-color: #c00000;
    display: flex;
    align-items: center;
    padding: 0 10px;
    .my-search-box {
        height: 36px;
        background-color: #fff;
        // border-radius: 18px;
        width: 100%;
        display: flex;
        justify-content: center;
        align-items: center;
        .palceholder {
            font-size: 15px;
            margin-left: 5px;
        }
    }
}
</style>