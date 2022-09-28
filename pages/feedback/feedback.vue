<template>
    <view :style="'height: ' + height + 'px;background-color: #f2f2f2;'" class="page">
        <textarea class="box" placeholder="想要什么新功能？使用体验不好？在这里吐槽吧！" @input="feedback"></textarea>
        <button class="submit" @tap="submit">提交</button>
        <view class="promt">如果你在使用时出现急需解决的问题</view>
        <view class="promt">
            也可以直接联系客服QQ:
            <text class="qq">1439552119</text>
        </view>
    </view>
</template>

<script>
export default {
    data() {
        return {
            height: 0,
            value: ''
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
        feedback(e) {
            let value = e.detail.value;
            this.setData({
                value: value
            });
        },

        submit() {
            let value = this.value;
        }
    }
};
</script>
<style>
.page::before {
    content: '';
    display: table;
}

.page {
    background-color: #f2f2f2;
    font-size: 28rpx;
    color: #666;
}

.box {
    box-sizing: border-box;
    width: 92%;
    margin: 30rpx auto;
    height: 240px;
    padding: 20rpx;
    border: 1px solid #e6e6e6;
    box-shadow: 0 0 2px #666;
}

.submit {
    width: 92%;
    margin: 0 auto 20rpx;
    height: 80rpx;
    text-align: center;
    line-height: 80rpx;
    border-radius: 20px;
    color: #ffffff;
    background-color: rgb(28, 211, 155);
}

.promt {
    width: 92%;
    margin: 8rpx auto;
    font-size: 24rpx;
}

.promt .qq {
    text-decoration: underline;
}
</style>
