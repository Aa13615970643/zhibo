<template>
    <view class="page" :style="'height:' + height + 'px'">
        <view class="message-list" @tap="messageDetail" v-for="(item, index) in list" :key="item.id">
            <view class="list-left">
                <image class="avatar" :src="item.avatar"></image>
                <view>
                    <view class="user">
                        <text>{{ item.name }}</text>
                        <text class="role" v-if="item.status">官方</text>
                    </view>
                    <view class="message">{{ item.message }}</view>
                </view>
            </view>

            <!-- 时间 -->

            <view class="time">{{ item.time }}</view>
        </view>
    </view>
</template>

<script>
export default {
    data() {
        return {
            height: 0,
            list: [
                {
                    id: '21312-1231',
                    avatar: '/static/images/5.png',
                    name: '环信直播',
                    status: true,
                    message: '今日更新,体验新功能',
                    time: '2017-05-28'
                }
            ]
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
        messageDetail() {
            uni.navigateTo({
                url: `/pages/messageDetail/messageDetail`
            });
        }
    }
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

.message-list {
    width: 96%;
    box-sizing: border-box;
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin: 40rpx auto;
    font-size: 28rpx;
    padding: 20rpx;
    background-color: #fff;
}

.list-left {
    display: flex;
    justify-content: flex-start;
    align-items: center;
}

.message-list .avatar {
    width: 100rpx;
    height: 100rpx;
    border-radius: 50%;
}

.message-list .user {
    margin-left: 8rpx;
    font-weight: 600;
}

.message-list .message {
    margin-left: 8rpx;
    margin-top: 8rpx;
    color: #c1c1c1;
    width: 200px;
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 1;
    -webkit-box-orient: vertical;
}

.role {
    display: inline-block;
    padding: 4rpx 8rpx;
    font-size: 24rpx;
    border-radius: 4rpx;
    color: #ffffff;
    background-color: #15b111;
    margin-left: 8rpx;
}

.time {
    font-size: 24rpx;
    color: #c1c1c1;
}
</style>
