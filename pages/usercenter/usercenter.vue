<template>
    <view :style="'background:#f9f9f9;padding-bottom: 20rpx;min-height:' + height + 'px'">
        <view class="head">
            <view class="usercenter-head">
                <image src="/static/images/live-logo.png" mode="aspectFill" class="use-avatar" />
                <text>环信直播聊天室</text>
            </view>
        </view>
        <view class="list">
            <text>版本</text>
            <text>1.0.0</text>
        </view>
        <view class="list" @tap="set">
            <text>关于环信</text>
            <image class="icon more" src="/static/images/more.png"></image>
        </view>
    </view>
</template>

<script>
let app = getApp();
export default {
    data() {
        return {
            height: 0,
            imgUrl: '/static/images/3.png'
        };
    },
    onLoad() {},
    methods: {
        //设置
        set() {
            uni.navigateTo({
                url: '/pages/abount/abount'
            });
        }
    }
};
</script>
<style>
.head {
    width: 100%;
    height: 150px;
    font-size: 28rpx;
    background-color: #fff;
    margin-bottom: 20rpx;
}

.head-pictrue {
    width: 100%;
    height: 200px;
}

.usercenter-head {
    width: 100%;
    height: 168rpx;
    border-radius: 50%;
    position: relative;
    top: 50rpx;
    left: 50%;
    text-align: center;
    transform: translateX(-50%);
    background-color: #fff;
}

.use-avatar {
    width: 160rpx;
    height: 160rpx;
    border-radius: 50%;
    margin: 0 auto 19rpx;
    display: block;
}

.usercenter {
    margin-top: 80rpx;
    width: 100%;
    box-sizing: border-box;
    padding: 20rpx;
    overflow: hidden;
    text-overflow: ellipsis;
    text-align: center;
    display: -webkit-box;
    -webkit-line-clamp: 1;
    -webkit-box-orient: vertical;
}

.ID {
    width: 100%;
    text-align: center;
    font-size: 28rpx;
}

.myfa {
    display: inline-block;
    font: normal normal normal 18px/1 fa !important;
    font-size: inherit;
    text-rendering: auto;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
}

.gift {
    text-align: center;
    font-size: 28rpx;
    color: #999;
    margin: 20rpx;
}

.more {
    width: 16px;
    height: 16px;
}

.list {
    width: 100%;
    font-size: 28rpx;
    margin: 1rpx auto;
    background: #ffffff;
    display: flex;
    justify-content: space-between;
    align-items: center;
    box-sizing: border-box;
    padding: 20rpx;
    color: #1e3c44;
}

.change {
    position: absolute;
    top: 205px;
    right: 10rpx;
    width: 140rpx;
    height: 60rpx;
    border-radius: 6rpx;
    background-color: rgba(0, 0, 0, 0.4);
    color: #ffffff;
    font-size: 28rpx;
    text-align: center;
    line-height: 60rpx;
}
</style>
