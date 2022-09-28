<template>
    <view class="page" :style="'height: ' + height + 'px;'">
        <view class="title">修改用户名</view>
        <input class="name" placeholder="用户名不长于8个字" @confirm="name" focus confirm-type="确定" @input="change" />
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
        change(e) {
            let value = e.detail.value;
            this.setData({
                value: value
            });
        },

        name(event) {
            let value = this.value;
            console.log(value);
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
}

.title {
    margin: 20rpx;
    text-align: center;
    color: #666666;
}

.name {
    width: 96%;
    box-sizing: border-box;
    margin: auto;
    height: 72rpx;
    padding: 8rpx 12rpx;
    background-color: #fff;
    color: #333333;
}
</style>
