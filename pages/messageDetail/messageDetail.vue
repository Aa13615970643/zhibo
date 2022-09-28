<template>
    <view class="page" :style="'height:' + height + 'px'">
        <view class="messagee-box">
            <!-- 信息 -->
            <view class="card">
                <image class="avatar" src="/static/images/3.png" mode="aspectFill"></image>
                <view class="message">
                    吃鸡游戏免下载，打开网页就能玩！ 低配电脑玩吃鸡？So Easy！吃鸡网页版，只要电脑能开机！达到3级即可直接进入吃鸡决赛圈！熊猫游戏平台，欢乐无极限！
                    <view class="triangle"></view>
                </view>
            </view>
            <!-- 时间 -->
            <view class="date">
                <view class="time">2017-05-21 08:09:22</view>
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
    methods: {}
};
</script>
<style>
.page {
    background: #f2f2f2;
}

.page::before {
    content: '';
    display: table;
}

.messagee-box {
    margin-top: 40rpx;
}

.card {
    display: flex;
    justify-content: flex-start;
    align-items: flex-start;
    width: 100%;
}

.avatar {
    width: 100rpx;
    height: 100rpx;
    border-radius: 50%;
    margin-left: 20rpx;
}

.message {
    width: 60%;
    box-sizing: border-box;
    position: relative;
    padding: 20rpx;
    margin-left: 40rpx;
    margin-top: 40rpx;
    font-size: 28rpx;
    background-color: #fff;
}

.triangle {
    position: absolute;
    width: 0;
    height: 0;
    left: -14px;
    top: 4rpx;
    border-top: 10px solid transparent;
    border-right: 20px solid #ffffff;
    border-bottom: 10px solid transparent;
}

.date {
    display: flex;
    justify-content: center;
    margin-top: 20rpx;
}

.time {
    padding: 4px 10px;
    background-color: rgba(0, 0, 0, 0.2);
    color: #ffffff;
    border-radius: 20px;
    font-size: 24rpx;
}
</style>
