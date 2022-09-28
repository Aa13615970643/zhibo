<template>
    <view>
        <block name="giftModa">
            <view class="giftModa">
                <view class="mask" @tap="closeMask"></view>
                <!-- 观众送礼模块 -->
                <view v-if="identity !== 'compere'" style="width: 100%; padding: 20rpx; font-size: 15px; color: rgba(255, 255, 255, 0.8)">
                    <image src="/static/images/gift@2x.png" style="width: 13px; height: 13px; margin-right: 10px" />
                    <text class="giftModa-item">礼物</text>
                    <view style="margin: 5% 0">
                        <view
                            :class="'gift-item ' + (showGiftId === giftData.key && 'active')"
                            :data-giftid="giftData"
                            @tap="inselected"
                            v-for="(giftData, index) in giftData"
                            :key="giftData.value"
                        >
                            <image class="giftImg" :src="giftData.url" />

                            <text>{{ giftData.name }}</text>
                        </view>
                    </view>
                    <view style="float: right" v-if="showGivesModa">
                        <text class="gift-text">{{ giftName }}</text>
                        <form @submit="giftSubmit">
                            <view class="section">
                                <input
                                    class="input_input"
                                    name="giftNum"
                                    :value="giftNumValue"
                                    maxlength="3"
                                    placeholder="1"
                                    confirm-type="send"
                                    type="number"
                                    style="max-width: 50px; padding-left: 0; text-align: center; color: #000000"
                                />
                            </view>
                            <view class="btn-area">
                                <button
                                    formType="submit"
                                    plain
                                    style="
                                        width: 180rpx;
                                        background: linear-gradient(325deg, rgba(90, 93, 208, 1) 0%, rgba(4, 174, 240, 1) 100%);
                                        border-radius: 15px;
                                        color: #fff;
                                        font-size: 13px;
                                    "
                                >
                                    赠送
                                </button>
                            </view>
                        </form>
                    </view>
                </view>
                <!-- 主播查看礼物模块 -->
                <view v-else style="width: 100%; padding: 20rpx; font-size: 15px; color: rgba(255, 255, 255, 0.8)">
                    <image src="/static/images/gift@2x.png" style="width: 13px; height: 13px; margin-right: 10px" />
                    <text class="giftModa-item">礼物记录</text>
                    <view class="gift-record">
                        <text class="gf-text">共{{ 0 }}份</text>
                        <text class="gf-text">打赏人数{{ 0 }}人</text>
                    </view>
                </view>
            </view>
        </block>
    </view>
</template>

<script></script>
<style>
.giftModa {
    position: absolute;
    bottom: 0;
    display: flex;
    width: 100%;
    height: 50%;
    background: rgba(0, 0, 0, 0.7);
    z-index: 888;
    color: #ffffff;
}

.mask {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 50%;
}

.giftImg {
    width: 80rpx;
    height: 80rpx;
    display: block;
    margin: 20rpx auto;
}

.gift-item {
    display: inline-block;
    height: 180rpx;
    width: 24%;
    /* padding: 20rpx; */
    text-align: center;
}

.active {
    box-shadow: rgb(11, 234, 235) 0px 0px 10px inset;
}

.btn-area,
.section {
    display: inline-block;
}

.section {
    margin-right: 10px;
}

.gift-text {
    margin-right: 10px;
}

.gift-record {
    position: absolute;
    right: 0;
    top: 11px;
    color: rgba(255, 255, 255, 0.5);
}
.gift-record .gf-text {
    margin-right: 10px;
}
</style>
