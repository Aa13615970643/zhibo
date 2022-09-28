<template>
    <view class="page">
        <view class="section" v-for="(item, index) in liveroomsList" :key="item.labelId">
            <!-- parse <template is="activeCard" :data="data: item.data"></template> -->
            <block name="activeCard">
                <view class="card">
                    <view class="card-item" v-for="(cardData, index) in item.data" :key="cardData.userId">
                        <view v-if="cardData.status === 'offline'" style="height: 320rpx" :data-liveroom="cardData" @tap="createdLive">
                            <image :src="cardData.url" class="activeCard-bg"></image>
                            <!-- 立即开播状态 -->
                            <image class="open-live" src="/static/images/lijikaibo.png" />
                        </view>

                        <!-- 直播中（无法点击） -->

                        <view v-else>
                            <view class="off-bg"></view>
                            <image :src="cardData.cover" mode="widthFix" class="pictrue"></image>
                            <image class="off-live" src="/static/images/noChange.png" />
                            <view class="userinfo">
                                <text class="username">{{ cardData.owner }}</text>
                                <view class="count">
                                    <text style="margin-left: 4rpx">{{ cardData.affiliations_count }}正在看</text>
                                </view>
                            </view>
                        </view>
                    </view>
                </view>
            </block>
        </view>
    </view>
</template>

<script>
const app = getApp();
export default {
    data() {
        return {
            width: 0,
            height: 0,
            liveroomsList: [],
            nickName: '',
            myUserName: '',

            cardData: {
                userId: '',
                status: '',
                url: '',
                cover: '',
                owner: '',
                affiliations_count: ''
            }
        };
    },
    onLoad() {
        let self = this;
        self.getLiveRooms(6, '', callback);
        let userName = uni.getStorageSync('userName');
        let userInfo = JSON.parse(userName);
        console.log('userInfo>>', userInfo);
        self.setData({
            myUserName: userInfo.userName,
            nickName: userInfo.nickName
        });
        uni.getSystemInfo({
            success: function (res) {
                console.log('res:', res);
            }
        });

        function callback(res) {
            let list = res.data.entities;
            self.cursor = res.data.cursor;
            self.setData({
                liveroomsList: [
                    {
                        data: list
                    }
                ]
            });
        }
    },
    onPullDownRefresh() {
        let self = this;
        self.getLiveRooms(6, self.cursor, callback);

        function callback(res) {
            if (!res.data.entities) {
                uni.showToast({
                    title: '已无更多数据',
                    icon: 'none',
                    duration: 2000
                });
                uni.stopPullDownRefresh();
                return;
            }

            let list = res.data.entities;
            let currentList = list.concat(self.liveroomsList[0].data);
            self.cursor = res.data.cursor;
            self.setData({
                liveroomsList: [
                    {
                        label: '热门直播',
                        labelId: '10001',
                        data: currentList
                    }
                ]
            });
            console.log('list..', list);
            uni.stopPullDownRefresh();
        }
    },
    methods: {
        getLiveRooms(limit, cursor, callback) {
            let self = this;
            uni.request({
                url: 'https://a1.easemob.com/appserver/liverooms',
                data: {
                    limit: limit,
                    cursor: cursor
                },
                header: {
                    'content-type': 'application/json',
                    Authorization: 'Bearer ' + getApp().globalData.token
                },

                success(res) {
                    callback(res);
                },

                fail(e) {
                    callback(e);
                }
            });
        },

        createdLive(e) {
            let self = this;
            let liveroom = e.currentTarget.dataset.liveroom;
            let myUserName = this.myUserName;
            console.log('username>>>', myUserName, 'liveroom>>>', liveroom);
            uni.request({
                url: `https://a1.easemob.com/appserver/liverooms/${liveroom.id}/users/${myUserName}/ongoing`,
                method: 'POST',
                header: {
                    'content-type': 'application/json',
                    Authorization: 'Bearer ' + getApp().globalData.token
                },

                success(res) {
                    console.log('开播成功>>>', res);
                    self.goLive(res.data);
                },

                fail(e) {
                    console.log('err>>>', e);
                }
            });
        },

        goLive(res) {
            let roomInfo = res; // 进入直播间开始直播

            uni.WebIM.conn.joinChatRoom({
                roomId: res.id,
                success: successFun,
                error: errorFun
            });

            function successFun(res) {
                console.log('res>>>', res);
                uni.navigateTo({
                    url: `/pages/live/live?id=${roomInfo.id}&name=${roomInfo.name}&owner=${roomInfo.owner}&audience=${roomInfo.affiliations_count}&identity=compere`
                });
            }

            function errorFun(e) {
                console.log('加入失败', e);
            }
        }
    }
};
</script>
<style>
/* @import '../../template/card/card.css'; */
.page {
    background-color: #f2f2f2;
}

.head-swiper {
    height: 400rpx;
}

.activeCard-bg {
    width: 100%;
    height: 100%;
}

.head-title {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 40px;
    background: rgba(0, 0, 0, 0.4);
    display: flex;
    justify-content: flex-start;
    align-items: center;
}

.head-live {
    width: 48px;
    height: 48px;
    margin: 0 40rpx 0 30rpx;
}

.head-title .title {
    font-size: 28rpx;
    color: #fff;
}

.section {
    padding-bottom: 20rpx;
    border-bottom: 1px solid #e5e5e5;
    font-size: 28rpx;
}

.section .label-name {
    box-sizing: border-box;
    margin: 30rpx auto;
    padding: 0 30rpx;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.section .more {
    width: 16px;
    height: 16px;
}
</style>
