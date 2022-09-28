<template>
    <view class="page" :style="'min-height: ' + height + 'px'">
        <view class="box">
            <view class="list">
                <image src="/static/images/live.png" class="live"></image>
                <image src="/static/images/2.png" class="cover" @tap="detail"></image>
                <view class="info">
                    <view class="title">王者荣耀</view>
                    <view class="user">
                        <view class="name">
                            <text>
                                主播 :
                                <text>千与千寻</text>
                            </text>
                        </view>
                        <view class="people">
                            人气 :
                            <text>12w</text>
                        </view>
                    </view>
                </view>
            </view>
        </view>
    </view>
</template>

<script>
export default {
    data() {
        return {
            height: 0
        };
    },
    onLoad() {
        let self = this;
        uni.getSystemInfo({
            success: function (res) {
                self.setData({
                    height: res.windowHeight
                });
            }
        });
    },
    methods: {
        detail() {
            uni.navigateTo({
                url: '/pages/live/live'
            });
        }
    }
};
</script>
<style>
.page {
    background-color: #f9f9f9;
    font-size: 28rpx;
}

.page::before {
    content: '';
    display: table;
}

.box {
    width: 96%;
    margin: 20rpx auto;
}

.list {
    width: 100%;
    position: relative;
    box-sizing: border-box;
    display: flex;
    justify-content: flex-start;
    align-items: flex-start;
    padding-bottom: 20rpx;
    border-bottom: 1px solid #e6e6e6;
}

.live {
    position: absolute;
    width: 48px;
    height: 48px;
    top: -10px;
    left: 4px;
}

.cover {
    width: 50%;
    height: 100px;
    border-radius: 8rpx;
}

.info {
    margin-left: 20rpx;
}

.info .title {
    font-size: 30rpx;
    width: 100%;
    display: -webkit-box;
    -webkit-line-clamp: 1;
    -webkit-box-orient: vertical;
    overflow: hidden;
    text-overflow: ellipsis;
}

.info .user {
    margin-top: 40rpx;
    color: #999;
}

.info .name {
    width: 100%;
    display: -webkit-box;
    -webkit-line-clamp: 1;
    -webkit-box-orient: vertical;
    overflow: hidden;
    text-overflow: ellipsis;
}

.info .people {
    margin-top: 10rpx;
    width: 100%;
    display: -webkit-box;
    -webkit-line-clamp: 1;
    -webkit-box-orient: vertical;
    overflow: hidden;
    text-overflow: ellipsis;
}
</style>
