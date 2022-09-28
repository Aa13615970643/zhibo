<template>
    <view>
        <block name="memberListModa">
            <view class="memberListModa">
                <view class="mask" @tap="closeMask"></view>
                <!-- 观众视角模块 -->
                <view class="member-list" v-if="type !== 'compere'">
                    <image src="/static/images/user2.png" style="width: 15px; height: 15px; margin-right: 10px" />
                    <text class="memberListModa-item">观众{{ affiliations_count }}</text>
                    <!-- 人员列表 -->
                    <view class="child-view">
                        <view :data-giftid="memberList" style="height: 80rpx; display: flex; align-items: center" v-for="(memberList, index) in list" :key="index">
                            <image src="/static/images/head.png" style="width: 28px; height: 28px; border-radius: 50%; margin-right: 10px" />

                            <text>{{ memberList.member || memberList.owner }}</text>

                            <text style="color: rgb(0, 195, 255); margin-left: 10px">{{ memberList.owner && '管理员' }}</text>
                        </view>
                    </view>
                </view>
                <!-- 主播视角 -->
                <view class="weui-tab-member" v-else>
                    <view class="weui-navbar-member">
                        <block v-for="(item, index) in tabs" :key="index">
                            <view :id="index" :class="'weui-navbar__item-member ' + (activeIndex == index ? 'weui-bar__item_on-member' : '')" @tap="tabClick">
                                <view class="weui-navbar__title-member">{{ item }}</view>
                            </view>
                        </block>
                        <view
                            class="weui-navbar__slider-member"
                            :style="'left: ' + sliderLeft + 'px; transform: translateX(' + sliderOffset + 'px); -webkit-transform: translateX(' + sliderOffset + 'px);'"
                        ></view>
                    </view>
                    <!-- 观众 -->
                    <view class="weui-tab__panel-member">
                        <view class="weui-tab__content-member" v-if="activeIndex == 0" style="top: 100rpx; padding: 10px">
                            <view style="float: right; padding-right: 10px">
                                <text>房间禁言</text>
                                <switch @change="changeSpeech" :checked="switchChecked" />
                            </view>
                            <view :data-giftid="memberList" style="height: 80rpx; display: flex; align-items: center" v-for="(memberList, index) in list" :key="index">
                                <image src="/static/images/head.png" style="width: 28px; height: 28px; border-radius: 50%; margin-right: 10px" />

                                <text>{{ memberList.member || memberList.owner }}</text>

                                <text style="color: rgb(0, 195, 255); margin-left: 10px">{{ memberList.owner && '管理员' }}</text>
                            </view>
                        </view>
                    </view>
                    <!-- 白名单 -->
                    <view class="weui-tab__panel">
                        <view class="weui-tab__content-member" v-if="activeIndex == 1" style="top: 100rpx; padding: 10px">
                            <view :data-giftid="memberList" style="height: 80rpx; display: flex; align-items: center" v-for="(memberList, index) in whiteList" :key="index">
                                <image src="/static/images/head.png" style="width: 28px; height: 28px; border-radius: 50%; margin-right: 10px" />

                                <text>{{ memberList.member || memberList.owner }}</text>

                                <text style="color: rgb(0, 195, 255); margin-left: 10px">
                                    {{ memberList.owner && '管理员' }}
                                </text>
                            </view>
                        </view>
                    </view>
                    <!-- 用户禁言 -->
                    <view class="weui-tab__panel" style="top: 100rpx; padding: 10px">
                        <view class="weui-tab__content-member" v-if="activeIndex == 2" style="top: 100rpx; padding: 10px">
                            <text>第一期暂未实现（我们将在第二期实现此功能）</text>
                        </view>
                    </view>
                </view>
            </view>
        </block>
    </view>
</template>

<script></script>
<style>
.memberListModa {
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

.member-list {
    width: 100%;
    padding: 20rpx;
    font-size: 15px;
    color: rgba(255, 255, 255, 0.8);
    overflow-y: auto;
}

.child-view {
    margin: 10px 0;
    padding: 10px 0;
}

.weui-tab-member {
    width: 100%;
    position: relative;
    height: 100%;
}

.weui-navbar-member {
    display: -webkit-box;
    display: -webkit-flex;
    display: flex;
    position: absolute;
    z-index: 500;
    top: 0;
    width: 100%;
    border-bottom: 1rpx solid #ccc;
}

.weui-navbar__item-member {
    position: relative;
    display: block;
    -webkit-box-flex: 1;
    -webkit-flex: 1;
    flex: 1;
    padding: 13px 0;
    text-align: center;
    font-size: 0;
}

.weui-navbar__item-member.weui-bar__item_on-member {
    color: #197ead;
}

.weui-navbar__slider-member {
    position: absolute;
    content: ' ';
    left: 0;
    bottom: 0;
    width: 8.5em;
    height: 3px;
    background-color: #197ead;
    -webkit-transition: -webkit-transform 0.3s;
    transition: -webkit-transform 0.3s;
    transition: transform 0.3s;
    transition: transform 0.3s, -webkit-transform 0.3s;
}

.weui-navbar__title-member {
    display: inline-block;
    font-size: 15px;
    max-width: 8em;
    width: auto;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    word-wrap: normal;
}
.weui-tab__panel-member {
    box-sizing: border-box;
    height: 100%;
    padding-top: 50px;
    overflow: auto;
    -webkit-overflow-scrolling: touch;
}

.weui-tab__content-member {
    text-align: center;
    background-color: rgba(0, 0, 0, 0.2);
    position: absolute;
    width: 100%;
}
</style>
