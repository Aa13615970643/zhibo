<template>
    <view :class="'weui-msg ' + extClass">
        <view class="weui-msg__icon-area">
            <icon :type="type" :size="size" v-if="type"></icon>
            <image :src="icon" mode="aspectFit" v-else-if="icon" />
        </view>
        <view class="weui-msg__text-area">
            <view class="weui-msg__title">{{ title }}</view>
            <view class="weui-msg__desc">
                {{ desc }}
                <slot name="desc" v-if="!desc"></slot>
            </view>
            <slot name="extend"></slot>
        </view>
        <view class="weui-msg__opr-area">
            <view class="weui-btn-area">
                <slot name="handle"></slot>
            </view>
        </view>
        <view class="weui-msg__extra-area">
            <view class="weui-footer">
                <slot name="footer"></slot>
            </view>
        </view>
    </view>
</template>

<script>
export default {
    data() {
        return {};
    },
    options: {
        addGlobalClass: true,
        multipleSlots: true
    },
    props: {
        title: {
            type: String,
            default: ''
        },
        type: {
            type: String,
            default: ''
        },
        icon: {
            type: String,
            default: ''
        },
        desc: {
            type: String,
            default: ''
        },
        extClass: {
            type: String,
            default: ''
        },
        size: {
            type: Number,
            default: 64
        }
    }
};
/***/
</script>
<style>
.weui-msg {
    padding-top: 36px;
    padding: calc(36px + constant(safe-area-inset-top)) constant(safe-area-inset-right) constant(safe-area-inset-bottom) constant(safe-area-inset-left);
    padding: calc(36px + env(safe-area-inset-top)) env(safe-area-inset-right) env(safe-area-inset-bottom) env(safe-area-inset-left);
    text-align: center;
    line-height: 1.4;
    min-height: 100%;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    background-color: #ffffff;
}
.weui-msg__link {
    color: #576b95;
    display: inline-block;
    vertical-align: baseline;
}
.weui-msg__icon-area {
    margin-bottom: 32px;
}
.weui-msg__text-area {
    margin-bottom: 32px;
    padding: 0 32px;
    flex: 1;
    line-height: 1.6;
}
.weui-msg__text-area:first-child {
    padding-top: 96px;
}
.weui-msg__title {
    margin-bottom: 5px;
    font-weight: 700;
    font-size: 22px;
    word-wrap: break-word;
    word-break: break-all;
}
.weui-msg__desc {
    font-size: 17px;
    color: rgba(0, 0, 0, 0.9);
    word-wrap: break-word;
    word-break: break-all;
    margin-bottom: 16px;
}
.weui-msg__desc-primary {
    font-size: 14px;
    color: rgba(0, 0, 0, 0.5);
    word-wrap: break-word;
    word-break: break-all;
    margin-bottom: 16px;
}
.weui-msg__opr-area {
    margin-bottom: 16px;
}
.weui-msg__opr-area .weui-btn-area {
    margin: 0 16px;
}
.weui-msg__opr-area .weui-btn + .weui-btn {
    margin-bottom: 16px;
}
.weui-msg__opr-area:last-child {
    margin-bottom: 96px;
}
.weui-msg__opr-area + .weui-msg__extra-area {
    margin-top: 48px;
}
.weui-msg__tips-area {
    margin-bottom: 16px;
    padding: 0 40px;
}
.weui-msg__opr-area + .weui-msg__tips-area {
    margin-bottom: 48px;
}
.weui-msg__tips-area:last-child {
    margin-bottom: 64px;
}
.weui-msg__tips {
    font-size: 12px;
    color: rgba(0, 0, 0, 0.5);
}
.weui-msg__extra-area {
    position: static;
    margin-bottom: 24px;
    font-size: 12px;
    color: rgba(0, 0, 0, 0.5);
}
.weui-msg__icon-area image {
    width: 190rpx;
    height: 190rpx;
}
</style>
