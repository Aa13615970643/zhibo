<template>
    <view :class="showClone ? 'weui-show' : 'weui-hidden'">
        <view class="weui-mask init" v-if="mask" @tap="close" data-type="tap"></view>
        <view :class="'weui-half-screen-dialog ' + extClass">
            <view class="weui-half-screen-dialog__hd">
                <view v-if="closabled" class="weui-half-screen-dialog__hd__side" @tap="close" data-type="close">
                    <view class="weui-icon-btn weui-icon-btn_close">关闭</view>
                </view>
                <view class="weui-half-screen-dialog__hd__main">
                    <block v-if="title">
                        <text class="weui-half-screen-dialog__title">{{ title }}</text>
                        <text class="weui-half-screen-dialog__subtitle">{{ subTitle }}</text>
                    </block>
                    <block v-else>
                        <view class="weui-half-screen-dialog__title"><slot name="title"></slot></view>
                    </block>
                </view>
                <view class="weui-half-screen-dialog__hd__side">
                    <view class="weui-icon-btn weui-icon-btn_more">更多</view>
                </view>
            </view>
            <view class="weui-half-screen-dialog__bd">
                <block v-if="title">
                    <view class="weui-half-screen-dialog__desc">{{ desc }}</view>
                    <view class="weui-half-screen-dialog__tips">{{ tips }}</view>
                </block>
                <slot name="desc" v-else></slot>
            </view>
            <view class="weui-half-screen-dialog__ft">
                <block v-if="buttons && buttons.length">
                    <button
                        :type="item.type"
                        :class="'weui-btn ' + item.className"
                        :data-index="index"
                        @tap="buttonTap"
                        v-for="(item, index) in buttons"
                        :key="item.text + item.index"
                    >
                        {{ item.text }}
                    </button>
                    <!-- <view wx:for="{{buttons}}" class="weui-dialog__btn {{}} {{item.extClass}}" ></view> -->
                </block>
                <slot name="footer" v-else></slot>
            </view>
        </view>
    </view>
</template>

<script>
export default {
    data() {
        return {
            showClone: false
        };
    },
    options: {
        multipleSlots: true,
        addGlobalClass: true
    },
    props: {
        closabled: {
            type: Boolean,
            default: true
        },
        title: {
            type: String,
            default: ''
        },
        subTitle: {
            type: String,
            default: ''
        },
        extClass: {
            type: String,
            default: ''
        },
        desc: {
            type: String,
            default: ''
        },
        tips: {
            type: String,
            default: ''
        },
        maskClosable: {
            type: Boolean,
            default: true
        },
        mask: {
            type: Boolean,
            default: true
        },
        show: {
            type: Boolean,
            default: false
        },
        buttons: {
            type: Array,
            default: () => []
        }
    },
    methods: {
        close: function close(e) {
            var type = e.currentTarget.dataset.type;

            if (this.maskClosable || type === 'close') {
                this.setData({
                    showClone: false
                });
                this.$emit('close');
            }
        },
        buttonTap: function buttonTap(e) {
            var index = e.currentTarget.dataset.index;
            this.$emit(
                'buttontap',
                {
                    detail: {
                        index: index,
                        item: this.buttons[index]
                    }
                },
                {}
            );
        }
    },
    watch: {
        show: {
            handler: function (newVal, oldVal) {
                this.showClone = newVal;
            },

            immediate: true
        }
    }
};
/***/
</script>
<style>
.weui-half-screen-dialog {
    position: fixed;
    left: 0;
    right: 0;
    bottom: 0;
    max-height: 75%;
    z-index: 5000;
    line-height: 1.4;
    background-color: #ffffff;
    border-top-left-radius: 12px;
    border-top-right-radius: 12px;
    overflow: hidden;
    padding: 0 24px;
    padding: 0 calc(24px + constant(safe-area-inset-right)) constant(safe-area-inset-bottom) calc(24px + constant(safe-area-inset-left));
    padding: 0 calc(24px + env(safe-area-inset-right)) env(safe-area-inset-bottom) calc(24px + env(safe-area-inset-left));
}
.weui-half-screen-dialog__hd {
    font-size: 8px;
    height: 8em;
    display: flex;
    align-items: center;
}
.weui-half-screen-dialog__hd .weui-icon-btn {
    position: absolute;
    top: 50%;
    -webkit-transform: translateY(-50%);
    transform: translateY(-50%);
}
.weui-half-screen-dialog__hd__side {
    position: relative;
    left: -8px;
}
.weui-half-screen-dialog__hd__main {
    flex: 1;
}
.weui-half-screen-dialog__hd__side + .weui-half-screen-dialog__hd__main {
    text-align: center;
    padding: 0 40px;
}
.weui-half-screen-dialog__hd__main + .weui-half-screen-dialog__hd__side {
    right: -8px;
    left: auto;
}
.weui-half-screen-dialog__hd__main + .weui-half-screen-dialog__hd__side .weui-icon-btn {
    right: 0;
}
.weui-half-screen-dialog__title {
    display: block;
    color: rgba(0, 0, 0, 0.9);
    font-weight: 700;
    font-size: 15px;
}
.weui-half-screen-dialog__subtitle {
    display: block;
    color: rgba(0, 0, 0, 0.5);
    font-size: 10px;
}
.weui-half-screen-dialog__bd {
    word-wrap: break-word;
    -webkit-hyphens: auto;
    hyphens: auto;
    overflow-y: auto;
}
.weui-half-screen-dialog__desc {
    padding-top: 4px;
    font-size: 17px;
    font-weight: 700;
    color: rgba(0, 0, 0, 0.9);
    line-height: 1.4;
}
.weui-half-screen-dialog__tips {
    padding-top: 16px;
    font-size: 14px;
    color: rgba(0, 0, 0, 0.3);
    line-height: 1.4;
}
.weui-half-screen-dialog__ft {
    padding: 40px 24px 32px;
    text-align: center;
}
.weui-half-screen-dialog__ft .weui-btn:nth-last-child(n + 2),
.weui-half-screen-dialog__ft .weui-btn:nth-last-child(n + 2) + .weui-btn {
    display: inline-block;
    vertical-align: top;
    margin: 0 8px;
    width: 120px;
}
.weui-icon-btn {
    background-color: transparent;
    background-repeat: no-repeat;
    background-position: 50% 50%;
    background-size: 100%;
    border: 0;
    outline: 0;
    font-size: 0;
}
.weui-icon-btn_goback {
    width: 12px;
    height: 24px;
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='24' viewBox='0 0 12 24'%3E  %3Cg fill='none' fill-rule='evenodd' transform='translate(-16 -20)'%3E    %3Cpath fill='%23FFF' d='M0 12C0 5.373 5.367 0 12 0h390c6.628 0 12 5.374 12 12v52H0V12z'/%3E    %3Cpath fill='%23000' fill-opacity='.9' d='M26 39.438L24.955 40.5l-7.666-7.79a1.02 1.02 0 0 1 0-1.42l7.666-7.79L26 24.563 18.682 32 26 39.438z'/%3E  %3C/g%3E%3C/svg%3E");
}
.weui-icon-btn_close {
    width: 24px;
    height: 24px;
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' width='24' height='24' viewBox='0 0 24 24'%3E  %3Cdefs%3E    %3Cpath id='33cf2e7b-22e9-42d7-9c56-a9f4a4e03565-a' d='M8 6.943L1.807.75.75 1.807 6.943 8 .75 14.193l1.057 1.057L8 9.057l6.193 6.193 1.057-1.057L9.057 8l6.193-6.193L14.193.75z'/%3E  %3C/defs%3E  %3Cg fill='none' fill-rule='evenodd' transform='translate(-16 -20)'%3E    %3Cpath fill='%23FFF' d='M0 12C0 5.373 5.367 0 12 0h390c6.628 0 12 5.374 12 12v52H0V12z'/%3E    %3Cuse fill='%23000' fill-opacity='.9' transform='translate(20 24)' xlink:href='%2333cf2e7b-22e9-42d7-9c56-a9f4a4e03565-a'/%3E  %3C/g%3E%3C/svg%3E");
}
.weui-icon-btn_more {
    width: 24px;
    height: 24px;
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='24' height='24' viewBox='0 0 24 24'%3E  %3Cg fill='none' fill-rule='evenodd' transform='translate(-374 -20)'%3E    %3Cpath fill='%23FFF' d='M0 12C0 5.373 5.367 0 12 0h390c6.628 0 12 5.374 12 12v52H0V12z'/%3E    %3Cpath fill='%23000' fill-opacity='.9' d='M380.75 32a1.75 1.75 0 1 1-3.5 0 1.75 1.75 0 0 1 3.5 0zm5.25-1.75a1.75 1.75 0 1 1 0 3.5 1.75 1.75 0 0 1 0-3.5zm7 0a1.75 1.75 0 1 1 0 3.5 1.75 1.75 0 0 1 0-3.5z'/%3E  %3C/g%3E%3C/svg%3E");
}
.weui-mask {
    position: fixed;
    z-index: 1000;
    top: 0;
    right: 0;
    left: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.6);
}
.weui-mask_transparent {
    position: fixed;
    z-index: 1000;
    top: 0;
    right: 0;
    left: 0;
    bottom: 0;
}
.weui-mask,
.weui-half-screen-dialog {
    transition: all 0.3s;
}
.weui-hidden .weui-mask {
    visibility: hidden;
    opacity: 0;
}
.weui-hidden .weui-half-screen-dialog {
    transform: translateY(100%);
}
.weui-show .weui-mask {
    opacity: 1;
    visibility: visible;
}
.weui-show .weui-half-screen-dialog {
    transform: translateY(0%);
}
</style>
