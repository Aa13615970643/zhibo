<template>
    <!-- slide-view/slide-view.wxml -->

    <view :class="'weui-slideview weui-movable-view ' + (icon ? 'weui-slideview_icon' : '') + ' ' + extClass" style="width: 100%; height: 100%">
        <view
            @transitionend="handler.transitionEnd"
            :show="show"
            :change:show="handler.showChange"
            :rebounce="rebounce"
            :change:rebounce="handler.rebounceChange"
            :duration="duration"
            :change:duration="handler.durationChange"
            :change:disable="handler.disableChange"
            :disable="disable"
            :change:prop="handler.sizeReady"
            :prop="size"
            @touchstart="handler.touchstart"
            @touchmove="handler.touchmove"
            @touchend="handler.touchend"
            class="weui-slideview__left left"
            style="width: 100%"
        >
            <slot></slot>
        </view>
        <view class="weui-slideview__right right">
            <view class="weui-slideview__buttons" style="height: 100%; width: 100%" v-if="buttonsClone && buttonsClone.length">
                <view :class="'btn weui-slideview__btn__wrp ' + item.className + ' ' + item.extClass" v-for="(item, index) in buttonsClone" :key="index">
                    <view @tap="handler.hideButton" :data-data="item.data" :data-index="index" class="weui-slideview__btn">
                        <text v-if="!icon">{{ item.text }}</text>
                        <image class="weui-slideview__btn__icon" v-else :src="item.src" />
                    </view>
                </view>
            </view>
        </view>
    </view>
</template>
<script module="handler" lang="wxs" src="./slideview.wxs"></script>
<script>
export default {
    data() {
        return {
            size: null,

            handler: {
                showChange: '',
                rebounceChange: '',
                durationChange: '',
                disableChange: '',
                sizeReady: ''
            },

            buttonsClone: []
        };
    },
    options: {
        addGlobalClass: true,
        multipleSlots: true
    },
    props: {
        extClass: {
            type: String,
            default: ''
        },
        buttons: {
            type: Array,
            default: () => []
        },
        disable: {
            type: Boolean,
            default: false
        },
        icon: {
            type: Boolean,
            default: false
        },
        show: {
            type: Boolean,
            default: false
        },
        duration: {
            type: Number,
            default: 350
        },
        throttle: {
            type: Number,
            default: 40
        },
        rebounce: {
            type: Number,
            default: 0
        }
    },
    mounted: function ready() {
        this.updateRight();
        this.addClassNameForButton();
    },
    methods: {
        updateRight: function updateRight() {
            var that = this;
            var data = this;
            var query = uni.createSelectorQuery().in(this);
            query
                .select('.left')
                .boundingClientRect(function (res) {
                    console.log('right res', res);
                    var btnQuery = uni.createSelectorQuery().in(that);
                    btnQuery
                        .selectAll('.btn')
                        .boundingClientRect(function (rects) {
                            console.log('btn rects', rects);
                            that.setData({
                                size: {
                                    buttons: rects,
                                    button: res,
                                    show: data.show,
                                    disable: data.disable,
                                    throttle: data.throttle,
                                    rebounce: data.rebounce
                                }
                            });
                        })
                        .exec();
                })
                .exec();
        },
        addClassNameForButton: function addClassNameForButton() {
            var _data = this;
            var buttons = _data.buttons;
            var icon = _data.icon;
            buttons.forEach(function (btn) {
                if (icon) {
                    btn.className = '';
                } else if (btn.type === 'warn') {
                    btn.className = 'weui-slideview__btn-group_warn';
                } else {
                    btn.className = 'weui-slideview__btn-group_default';
                }
            });
            this.setData({
                buttonsClone: buttons
            });
        },
        buttonTapByWxs: function buttonTapByWxs(data) {
            this.$emit(
                'buttontap',
                {
                    detail: data
                },
                {}
            );
        },
        hide: function hide() {
            this.$emit(
                'hide',
                {
                    detail: {}
                },
                {}
            );
        },
        showFun: function show() {
            this.$emit(
                'show',
                {
                    detail: {}
                },
                {}
            );
        },
        transitionEnd: function transitionEnd() {
            console.log('transitiion end');
        }
    },
    watch: {
        buttons: {
            handler: function observer(newVal) {
                this.buttonsClone = this.deepClone(this.buttons);
                this.addClassNameForButton();
            },

            immediate: true,
            deep: true
        }
    }
};
/***/
</script>
<style>
:host {
    width: 100%;
}
.weui-slideview {
    overflow: hidden;
    position: relative;
}
.weui-slideview {
    position: relative;
}
.weui-slideview__left {
    position: relative;
    z-index: 10;
}
.weui-slideview__right {
    position: absolute;
    z-index: 1;
    left: 100%;
    top: 0;
    height: 100%;
}
.weui-slideview__btn__wrp {
    position: absolute;
    left: 0;
    bottom: 0;
    text-align: center;
    min-width: 69px;
    height: 100%;
    white-space: nowrap;
}
.weui-slideview__btn {
    color: #ffffff;
    padding: 0 17px;
}
.weui-slideview__btn-group_default .weui-slideview__btn {
    background: #c7c7cc;
}
.weui-slideview__btn-group_default ~ .weui-slideview__btn-group_default:before {
    content: ' ';
    position: absolute;
    left: 0;
    top: 0;
    width: 1px;
    bottom: 0;
    border-left: 1rpx solid #ffffff;
    color: #ffffff;
}
.weui-slideview__btn-group_default:first-child:before {
    display: none;
}
.weui-slideview__btn-group_warn .weui-slideview__btn {
    background: #fe3b30;
}
.weui-slideview__btn-group_warn ~ .weui-slideview__btn-group_warn:before {
    content: ' ';
    position: absolute;
    left: 0;
    top: 0;
    width: 1px;
    bottom: 0;
    border-left: 1rpx solid #ffffff;
    color: #ffffff;
}
.weui-slideview__btn-group_warn:first-child:before {
    display: none;
}
.weui-slideview_icon .weui-slideview__btn__wrp {
    background: transparent;
    font-size: 0;
}
.weui-slideview_icon .weui-slideview__btn__wrp:after {
    content: '';
    width: 0;
    height: 100%;
    vertical-align: middle;
    display: inline-block;
}
.weui-slideview_icon .weui-slideview__btn__wrp:first-child {
    padding-left: 16px;
}
.weui-slideview_icon .weui-slideview__btn__wrp:last-child {
    padding-right: 8px;
}
.weui-slideview_icon .weui-slideview__btn {
    width: 48px;
    height: 48px;
    line-height: 48px;
    padding: 0;
    display: inline-block;
    vertical-align: middle;
    border-radius: 50%;
    background-color: #ffffff;
}
.weui-slideview_icon .weui-slideview__btn__icon {
    display: inline-block;
    vertical-align: middle;
    width: 22px;
    height: 22px;
}
</style>
