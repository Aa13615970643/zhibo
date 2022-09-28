<template>
    <view class="page" :style="'height: ' + height + 'px'">
        <view class="version">
            <view class="title">环信直播</view>
            <view class="text">版本号：1.0.0</view>
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
    methods: {}
};
</script>
<style>
.page {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: #f9f9f9;
}

.version {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}
.title {
    font-size: 40rpx;
    text-align: center;
    font-weight: 600;
    margin-bottom: 20rpx;
}

.pictrue {
    width: 200px;
    height: 100px;
    border-radius: 4px;
}

.text {
    font-size: 28rpx;
    text-align: center;
}
</style>
