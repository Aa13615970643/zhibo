<template>
    <view>
        <!-- 正在直播 -->
        <block v-if="cards == 'card'" name="card">
            <view class="card">
                <view class="card-item" :data-liveroom="cardData" @tap="golive" v-for="cardData in list" :key="cardData.id">
                    <image :src="cardData.cover" mode="widthFix" class="pictrue"></image>

                    <!-- 正在直播图标 -->

                    <view class="active">
                        <image class="active-icon" src="/static/images/live-icon.png" />
                        <text style="color: whit">正在直播</text>
                    </view>

                    <!-- <view class="title">{{cardData.title}}</view> -->

                    <view class="userinfo">
                        <text class="username">{{ cardData.owner }}</text>
                        <view class="count">
                            <text style="margin-left: 4rpx">{{ cardData.affiliations_count }}正在看</text>
                        </view>
                    </view>
                </view>
            </view>
        </block>
        <!-- 立即开播 -->
        <block v-if="cards == 'activeCard'" name="activeCard">
            <view class="card">
                <view class="card-item" v-for="cardData in list" :key="cardData.userId">
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
</template>

<script>
// template/card/card.js
export default {
    props:{
      list:{
         type:Array
      },
      cards:{
         type:String
      }
    },
    data() {
        return {
            cardData: {
                id: '',
                cover: '',
                owner: '',
                affiliations_count: '',
                userId: '',
                status: '',
                url: ''
            },

         /*    list: [] */
        };
    }
    /**
     * 生命周期函数--监听页面加载
     */,
    onLoad: function (options) {},
    /**
     * 生命周期函数--监听页面初次渲染完成
     */
    onReady: function () {},
    /**
     * 生命周期函数--监听页面显示
     */
    onShow: function () {},
    /**
     * 生命周期函数--监听页面隐藏
     */
    onHide: function () {},
    /**
     * 生命周期函数--监听页面卸载
     */
    onUnload: function () {},
    /**
     * 页面相关事件处理函数--监听用户下拉动作
     */
    onPullDownRefresh: function () {},
    /**
     * 页面上拉触底事件的处理函数
     */
    onReachBottom: function () {},
    /**
     * 用户点击右上角分享
     */
    onShareAppMessage: function () {},
    methods: {
          golive(e) {
            console.log('liveroom', e);
            let liveroom = e.currentTarget.dataset.liveroom; //进入直播间

            uni.WebIM.conn.joinChatRoom({
                roomId: liveroom.id,
                success: successFun,
                error: errorFun
            });

            function successFun(res) {
                uni.navigateTo({
                    url: `/pages/live/live?id=${liveroom.id}&name=${liveroom.name}&owner=${liveroom.owner}&audience=${liveroom.affiliations_count}`
                });
            }

            function errorFun(e) {
                console.log('加入失败', e);
            }
        },

        createdLive() {
            console.log('占位：函数 createdLive 未声明');
        }
    }
};
</script>
<style>
.card {
    width: 98%;
    margin: auto;
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-wrap: wrap;
}

.card-item {
    width: 49%;
    height: 320rpx;
    position: relative;
    background: #fff;
    border-radius: 6rpx;
    margin-bottom: 2%;
}

.card-item .active {
    position: absolute;
    top: 10rpx;
    left: 12rpx;
    padding: 4px 8px;
    border-radius: 8rpx;
    font-size: 24rpx;
    color: #ffffff;
    width: 70px;
    height: 15px;
    background: linear-gradient(325deg, rgba(90, 93, 208, 1) 0%, rgba(4, 174, 240, 1) 100%);
}

.active-icon {
    width: 11px;
    height: 11px;
    position: relative;
    right: 2px;
    top: 2px;
}

.card-item .pictrue {
    width: 100%;
    height: 110px !important;
}

.card-item .title {
    box-sizing: border-box;
    padding: 0 20rpx;
    margin: 10rpx 0;
    display: -webkit-box;
    overflow: hidden;
    text-overflow: ellipsis;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 1;
    font-size: 26rpx;
}

.card-item .userinfo {
    box-sizing: border-box;
    padding: 0 10rpx;
    margin-bottom: 10rpx;
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-size: 24rpx;
    color: #c1c1c1;
}

.card-item .username {
    width: 80px;
    display: -webkit-box;
    overflow: hidden;
    text-overflow: ellipsis;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 1;
}
.open-live {
    position: absolute;
    top: 40%;
    left: 20%;
    width: 222rpx;
    height: 66rpx;
    align-items: center;
}
.off-live {
    position: absolute;
    top: 40%;
    left: 35%;
    width: 124rpx;
    height: 78rpx;
    align-items: center;
    z-index: 10;
}
.off-bg {
    background-color: rgba(0, 0, 0, 0.7);
    z-index: 9;
    height: 320rpx;
    width: 100%;
    position: absolute;
    top: 0;
}
</style>
