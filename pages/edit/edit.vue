<template>
    <view class="page">
        <textarea placeholder="编辑直播公告亲~" maxlength="500" focus class="notice" @confirm="submit" @input="notice"></textarea>
        <!-- <button class="submit" bindtap='submit'>确定</button> -->
    </view>
</template>

<script>
export default {
    data() {
        return {
            value: ''
        };
    },
    methods: {
        notice(e) {
            let value = e.detail.value;
            this.setData({
                value: value
            });
        },

        submit() {
            let value = this.value;
            uni.setStorage({
                key: 'notice',
                data: value
            }); //Todo接口
        }
    }
};
</script>
<style>
.page {
    position: relative;
}

.notice {
    width: 100%;
    font-size: 28rpx;
    height: 100px;
    border-bottom: 1px solid #e6e6e6;
    box-sizing: border-box;
    padding: 20rpx;
}

.placeHolder {
    font-size: 28rpx;
    color: #c1c1c1;
}

.submit {
    position: fixed;
    bottom: 0;
    left: 50%;
    transform: translateX(-50%);
    width: 100px;
    color: #1e3c44;
    height: 40px;
}
</style>
