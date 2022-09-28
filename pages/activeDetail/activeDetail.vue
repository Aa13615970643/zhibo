<template>
    <view>
        <!-- parse <template is="activeCard" :data="data: list,activeName"></template> -->
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
</template>

<script>
export default {
    data() {
        return {
            activeName: '正在直播中',
            list: [],

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
    methods: {
        golive() {
            uni.navigateTo({
                url: `/pages/live/live`
            });
        },

        createdLive() {
            console.log('占位：函数 createdLive 未声明');
        }
    }
};
</script>
<style>
/* @import '../../template/card/card.css'; */
</style>
