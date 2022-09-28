<template>
    <view :style="'height: ' + height + 'px;background-color: #f2f2f2;'" class="page">
        <view class="list" @tap="userinfo">
            <text>个人信息</text>
            <image class="icon more" src="/static/images/more.png"></image>
        </view>
        <view class="list" @tap="feedback">
            <text>意见反馈</text>
            <image class="icon more" src="/static/images/more.png"></image>
        </view>
        <view class="list" @tap="notice">
            <text>平台公告</text>
            <image class="icon more" src="/static/images/more.png"></image>
        </view>
        <view class="list" @tap="about">
            <text>关于环信</text>
            <image class="icon more" src="/static/images/more.png"></image>
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
        //信息
        userinfo() {
            uni.navigateTo({
                url: '/pages/userinfo/userinfo'
            });
        },

        //反馈
        feedback() {
            uni.navigateTo({
                url: '/pages/feedback/feedback'
            });
        },

        //关于我们
        about() {
            uni.navigateTo({
                url: '/pages/abount/abount'
            });
        },

        notice() {
            uni.navigateTo({
                url: '/pages/notice/notice'
            });
        }
    }
};
</script>
<style>
.page::before {
    content: '';
    display: table;
}

.more {
    width: 16px;
    height: 16px;
}

.list {
    width: 100%;
    font-size: 28rpx;
    margin: 20rpx auto;
    background: #ffffff;
    display: flex;
    justify-content: space-between;
    align-items: center;
    box-sizing: border-box;
    padding: 20rpx;
    color: #1e3c44;
}
</style>
