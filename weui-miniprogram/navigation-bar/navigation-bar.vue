<template>
    <view :class="'weui-navigation-bar ' + extClass">
        <view :class="'weui-navigation-bar__placeholder ' + (ios ? 'ios' : 'android')" :style="'padding-top: ' + statusBarHeight + 'px;visibility: hidden;'"></view>
        <view
            :class="'weui-navigation-bar__inner ' + (ios ? 'ios' : 'android')"
            :style="
                'padding-top: ' + statusBarHeight + 'px; color: ' + color + ';background: ' + background + ';' + displayStyle + ';' + innerPaddingRight + ';' + innerWidth + ';'
            "
        >
            <view class="weui-navigation-bar__left" :style="leftWidth">
                <block v-if="back">
                    <view class="weui-navigation-bar__buttons">
                        <view @tap="backFun" class="weui-navigation-bar__button weui-navigation-bar__btn_goback"></view>
                    </view>
                </block>
                <block v-else>
                    <slot name="left"></slot>
                </block>
            </view>

            <view class="weui-navigation-bar__center">
                <view v-if="loading" class="weui-navigation-bar__loading">
                    <view class="weui-loading" :style="'width:' + size.width + 'rpx;height:' + size.height + 'rpx;'"></view>
                </view>
                <block v-if="title">
                    <text>{{ title }}</text>
                </block>
                <block v-else>
                    <slot name="center"></slot>
                </block>
            </view>

            <view class="weui-navigation-bar__right">
                <slot name="right"></slot>
            </view>
        </view>
    </view>
</template>

<script>
export default {
    data() {
        return {
            displayStyle: '',
            ios: '',
            statusBarHeight: '',
            innerWidth: '',
            innerPaddingRight: '',
            leftWidth: '',

            size: {
                width: '',
                height: ''
            }
        };
    },
    options: {
        multipleSlots: true,
        addGlobalClass: true
    },
    props: {
        extClass: {
            type: String,
            default: ''
        },
        title: {
            type: String,
            default: ''
        },
        background: {
            type: String,
            default: ''
        },
        color: {
            type: String,
            default: ''
        },
        back: {
            type: Boolean,
            default: true
        },
        loading: {
            type: Boolean,
            default: false
        },
        animated: {
            type: Boolean,
            default: true
        },
        show: {
            type: Boolean,
            default: true
        },
        delta: {
            type: Number,
            default: 1
        }
    },
    beforeMount: function attached() {
        var that = this;
        var isSupport = !!uni.getMenuButtonBoundingClientRect;
        var rect = uni.getMenuButtonBoundingClientRect ? uni.getMenuButtonBoundingClientRect() : null;
        uni.getSystemInfo({
            success: function success(res) {
                var ios = !!(res.system.toLowerCase().search('ios') + 1);
                that.setData({
                    ios: ios,
                    statusBarHeight: res.statusBarHeight,
                    innerWidth: isSupport ? 'width:' + rect.left + 'px' : '',
                    innerPaddingRight: isSupport ? 'padding-right:' + (res.windowWidth - rect.left) + 'px' : '',
                    leftWidth: isSupport ? 'width:' + (res.windowWidth - rect.left) + 'px' : ''
                });
            }
        });
    },
    methods: {
        _showChange: function _showChange(show) {
            var animated = this.animated;
            var displayStyle = '';

            if (animated) {
                displayStyle = 'opacity: ' + (show ? '1' : '0') + ';-webkit-transition:opacity 0.5s;transition:opacity 0.5s;';
            } else {
                displayStyle = 'display: ' + (show ? '' : 'none');
            }

            this.setData({
                displayStyle: displayStyle
            });
        },
        backFun: function back() {
            var data = this;
            uni.navigateBack({
                delta: data.delta
            });
            this.$emit(
                'back',
                {
                    detail: {
                        delta: data.delta
                    }
                },
                {}
            );
        }
    },
    watch: {
        show: {
            handler: function _showChange(show) {
                var animated = this.animated;
                var displayStyle = '';

                if (animated) {
                    displayStyle = 'opacity: ' + (show ? '1' : '0') + ';-webkit-transition:opacity 0.5s;transition:opacity 0.5s;';
                } else {
                    displayStyle = 'display: ' + (show ? '' : 'none');
                }

                this.setData({
                    displayStyle: displayStyle
                });
            },

            immediate: true
        }
    }
};
/***/
</script>
<style>
page {
    --height: 44px;
    --right: 190rpx;
}
.weui-navigation-bar {
    overflow: hidden;
}
.weui-navigation-bar .android {
    --height: 48px;
    --right: 222rpx;
}
.weui-navigation-bar__inner {
    position: fixed;
    top: 0;
    left: 0;
    z-index: 5001;
    height: var(--height);
    display: flex;
    align-items: center;
    padding-right: var(--right);
    width: calc(100% - var(--right));
}
.weui-navigation-bar__inner .weui-navigation-bar__left {
    position: relative;
    width: var(--right);
    padding-left: 16px;
    display: -webkit-box;
    display: -webkit-flex;
    display: flex;
    align-items: center;
    -webkit-box-pack: center;
}
.weui-navigation-bar__inner .weui-navigation-bar__left .weui-navigation-bar__btn {
    display: inline-block;
    vertical-align: middle;
    background-repeat: no-repeat;
}
.weui-navigation-bar__inner .weui-navigation-bar__left .weui-navigation-bar__btn_goback {
    font-size: 12px;
    width: 1em;
    height: 2em;
    background-image: url("data:image/svg+xml;charset=utf8,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='24' viewBox='0 0 12 24'%3E  %3Cpath fill-opacity='.9' fill-rule='evenodd' d='M10 19.438L8.955 20.5l-7.666-7.79a1.02 1.02 0 0 1 0-1.42L8.955 3.5 10 4.563 2.682 12 10 19.438z'/%3E%3C/svg%3E");
    background-position: 50% 50%;
    background-size: cover;
}
.weui-navigation-bar__inner .weui-navigation-bar__left .weui-navigation-bar__btn_goback:active {
    opacity: 0.5;
}
.weui-navigation-bar__inner .weui-navigation-bar__center {
    font-size: 17px;
    text-align: center;
    position: relative;
    flex: 1;
    display: -webkit-box;
    display: -webkit-flex;
    display: flex;
    align-items: center;
    -webkit-box-pack: center;
    -webkit-justify-content: center;
    justify-content: center;
}
.weui-navigation-bar__inner .weui-navigation-bar__loading {
    font-size: 0;
}
.weui-navigation-bar__inner .weui-navigation-bar__loading .weui-loading {
    margin-left: 0;
}
.weui-navigation-bar__inner .weui-navigation-bar__right {
    margin-right: 16px;
}
.weui-navigation-bar__placeholder {
    height: var(--height);
    background: #f8f8f8;
    position: relative;
    z-index: 50;
}
</style>
