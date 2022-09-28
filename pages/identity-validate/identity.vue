<template>
    <view class="page">
        <view class="head">完善身份信息</view>
        <!-- 验证区 -->
        <view class="card">
            <view class="tel">
                <text>真实姓名：</text>
                <input class="write" focus />
            </view>
            <view class="tel">
                <text>身份证号码：</text>
                <input class="write" />
            </view>
            <view class="code">
                <text>直播类别：</text>
                <input class="write" focus placeholder="请填写正确类别" />
            </view>
        </view>
        <button class="next" @tap="next">完成</button>
    </view>
</template>

<script>
// pages/identity-validate/identity.js
export default {
    data() {
        return {};
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
        next() {
            console.log('占位：函数 next 未声明');
        }
    }
};
</script>
<style>
.page {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: #f9f9f9;
    font-size: 28rpx;
}

.head {
    width: 100%;
    height: 80rpx;
    line-height: 80rpx;
    margin-top: 20rpx;
    text-align: center;
    font-size: 32rpx;
    background-color: #fff;
}

.card {
    width: 92%;
    margin: 20rpx auto;
    box-sizing: border-box;
    padding: 10rpx;
    background: #fff;
}

.myfa {
    display: inline-block;
    font: normal normal normal 40px/1 fa !important;
    font-size: inherit;
    text-rendering: auto;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
}

.tel {
    padding: 30rpx 20rpx 8rpx;
    border-bottom: 1px solid #e6e6e6;
    position: relative;
    display: flex;
    align-items: center;
}

.tel .write {
    width: 70%;
    margin-left: 20rpx;
    height: 60rpx;
    line-height: 60rpx;
}

.code {
    padding: 30rpx 20rpx 8rpx;
    display: flex;
    align-items: center;
}

.next {
    width: 92%;
    margin: 40rpx auto;
    height: 80rpx;
    line-height: 80rpx;
    text-align: center;
    color: #fff;
    background: #15b111;
    font-size: 28rpx;
}
</style>
