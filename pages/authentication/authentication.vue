<template>
    <view class="page">
        <view class="content">
            <view style="text-align: center">
                阅读
                <text class="agreement" @tap="agreement">《环信直播主播条议》</text>
            </view>
            <view class="btn" @tap="validate">已阅读,开始实名认证</view>
        </view>
    </view>
</template>

<script>
export default {
    data() {
        return {};
    },
    methods: {
        agreement() {
            uni.navigateTo({
                url: '/pages/notice/notice'
            });
        },

        validate() {
            uni.navigateTo({
                url: '/pages/validate/validate'
            });
        }
    }
};
</script>
<style>
.page {
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    background-color: #f9f9f9;
    font-size: 28rpx;
}

.content {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

.agreement {
    color: #15b111;
}

.btn {
    padding: 8rpx 20rpx;
    color: #ffffff;
    background-color: #15b111;
    border-radius: 20px;
    margin: auto;
    text-align: center;
    margin-top: 40rpx;
}
</style>
