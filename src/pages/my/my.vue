<template>
    <view class="my-container">
        <!-- 用户未登录时，显示登录组件 -->
        <my-login v-if="!token"></my-login>

        <!-- 用户登录后，显示用户信息组件 -->
        <my-userinfo v-else></my-userinfo>
    </view>
</template>

<script>
import MyLogin from "../../components/my-login/my-login.vue";
import MyUserinfo from "../../components/my-userinfo/my-userinfo.vue";
import { mapState } from 'vuex'
import badgeMix from '../../mixins/tabbar-badge'
export default {
    // 将 badgeMix 混入到当前的页面中进行使用
    mixins: [badgeMix],
    components: {
        MyLogin,
        MyUserinfo
    },
    
    computed: {
        ...mapState('m_user', ['token'])
    }
}
</script>
<style lang="scss">
page,
.my-container {
  height: 100%;
}
</style>