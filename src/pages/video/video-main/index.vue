<template>
    <scroll-view scroll-y enable-flex class="video_main" @scrolltolower="handleScrollToLower">
        <view class="video_item" v-for="item in videoList" :key="item.id" @click="handleGoVideo(item)">
            <image :src="item.img" mode="widthFix"></image>
        </view>
    </scroll-view>
</template>
<script>
export default {
    data() {
        return {
            videoList: [],
            hasMore: true
        }
    },
    props: {
        urlobj: Object
    },
    mounted() {
        this.getList()
    },
    watch: {
        urlobj() {
          this.videoList = []
            this.getList()
        }
    },
    methods: {
        async getList() {
            const { data: res } = await this.request({
                url: this.urlobj.url,
                data: this.urlobj.params
            })
            if (res.res.videowp.length === 0) {
                this.hasMore = false
                uni.showToast({
                    title: '没有更多数据了',
                    icon: 'none'
                })
                return
            }
            this.videoList = [...this.videoList, ...res.res.videowp]
        },
        // 分页事件
        handleScrollToLower() {
            if (this.hasMore) {
                this.urlobj.params.skip += this.urlobj.params.limit
                
                this.getList()
            } else {
                uni.showToast({
                    title: '没有更多数据了',
                    icon: 'none'
                })
            }
        },
        // 点击播放视频
        handleGoVideo(item) {
          // 将数据存到全局共享
          getApp().globalData.video = item
          // 页面跳转
          uni.navigateTo({
            url: '/pages/videoPlay/index'
          })
        }
    }
}
</script>
<style lang="scss" scoped>
.video_main {
    display: flex;
    flex-wrap: wrap;
    height: calc(100vh - 36px);
    .video_item {
        width: 33.33%;
        border: 5rpx solid #fff;
    }
}
</style>