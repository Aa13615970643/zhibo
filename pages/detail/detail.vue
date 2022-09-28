<template>
    <view class="page" :style="'min-height:' + height + 'px'">
        <!-- parse <template is="card" :data="data: list"></template> -->
        <block name="card">
            <view class="card">
                <view class="card-item" :data-liveroom="cardData" @tap="golive" v-for="(cardData, index) in list" :key="cardData.id">
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
    </view>
</template>

<script>
const app = getApp();
export default {
    data() {
        return {
            height: 0,
            list: [],

            cardData: {
                id: '',
                cover: '',
                owner: '',
                affiliations_count: ''
            }
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
        golive() {
            uni.navigateTo({
                url: `/pages/live/live`
            });
        }
    }
};
</script>
<style>
/* @import '../../template/card/card.css'; */

.page {
    padding-top: 20rpx;
    background-color: #f2f2f2;
}
</style>
