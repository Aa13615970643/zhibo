<template>
    <view>
        <view v-if="mask" :class="'weui-mask ' + (showClone ? '' : 'weui-mask_hidden') + ' ' + maskClass" @tap="closeActionSheet"></view>
        <view :class="'weui-actionsheet ' + (showClone ? 'weui-actionsheet_toggle' : '') + ' ' + extClass">
            <!-- 标题 -->
            <block v-if="title">
                <view class="weui-actionsheet__title">
                    <view class="weui-actionsheet__title-text">{{ title }}</view>
                </view>
            </block>
            <slot name="title" v-else></slot>
            <view
                :class="!showCancel && index === actionsClone.length - 1 ? 'weui-actionsheet__action' : 'weui-actionsheet__menu'"
                v-for="(actionItem, index) in actionsClone"
                :key="index"
            >
                <block v-if="utils.isNotSlot(actionItem)">
                    <view
                        :class="'weui-actionsheet__cell ' + (item.type === 'warn' ? 'weui-actionsheet__cell_warn' : '')"
                        :data-groupindex="index"
                        :data-index="actionIndex"
                        :data-value="item.value"
                        @tap="buttonTap"
                        v-for="(item, actionIndex) in actionItem"
                        :key="item.text"
                    >
                        {{ item.text }}
                    </view>
                </block>

                <slot :name="actionItem" v-else></slot>
            </view>
            <!-- 取消按钮 -->
            <view class="weui-actionsheet__action" v-if="showCancel">
                <view class="weui-actionsheet__cell" data-type="close" id="iosActionsheetCancel" @tap="closeActionSheet">{{ cancelText }}</view>
            </view>
        </view>
    </view>
</template>
<script module="utils" lang="wxs">
var join = function(a,b) {
    return a+b
};
var isNotSlot = function(v) {
    return typeof v !== 'string'
}
module.exports = {
    join: join,
    isNotSlot: isNotSlot
}
</script>
<script>
export default {
    data() {
        return {
            actionIndex: 0,
            actionItem: [],
            actionsClone: [],
            showClone: false
        };
    },
    options: {
        multipleSlots: true,
        addGlobalClass: true
    },
    props: {
        title: {
            type: String,
            default: ''
        },
        showCancel: {
            type: Boolean,
            default: true
        },
        cancelText: {
            type: String,
            default: '取消'
        },
        maskClass: {
            type: String,
            default: ''
        },
        extClass: {
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
        actions: {
            type: Array,
            default: () => []
        }
    },
    methods: {
        _groupChange: function _groupChange(e) {
            if (e.length > 0 && typeof e[0] !== 'string' && !(e[0] instanceof Array)) {
                this.setData({
                    actionsClone: [this.actions]
                });
            }
        },
        buttonTap: function buttonTap(e) {
            var _e$currentTarget$data = e.currentTarget.dataset;
            var value = _e$currentTarget$data.value;
            var groupindex = _e$currentTarget$data.groupindex;
            var index = _e$currentTarget$data.index;
            this.$emit('actiontap', {
                detail: {
                    value: value,
                    groupindex: groupindex,
                    index: index
                }
            });
        },
        closeActionSheet: function closeActionSheet(e) {
            var type = e.currentTarget.dataset.type;

            if (this.maskClosable || type) {
                this.setData({
                    showClone: false
                });
                this.$emit('close');
            }
        }
    },
    watch: {
        actions: {
            handler: function _groupChange(e) {
                this.actionsClone = this.deepClone(this.actions);
                if (e.length > 0 && typeof e[0] !== 'string' && !(e[0] instanceof Array)) {
                    this.setData({
                        actionsClone: [this.actions]
                    });
                }
            },

            immediate: true,
            deep: true
        },

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
.weui-actionsheet {
    position: fixed;
    left: 0;
    bottom: 0;
    transform: translate(0, 100%);
    backface-visibility: hidden;
    z-index: 5000;
    width: 100%;
    background-color: #eae7e8;
    transition: transform 0.3s;
    border-top-left-radius: 12px;
    border-top-right-radius: 12px;
    overflow: hidden;
}
.weui-actionsheet__title {
    position: relative;
    height: 56px;
    padding: 0 24px;
    display: flex;
    justify-content: center;
    flex-direction: column;
    text-align: center;
    font-size: 12px;
    color: rgba(0, 0, 0, 0.5);
    line-height: 1.4;
    background: #ffffff;
}
.weui-actionsheet__title:before {
    content: ' ';
    position: absolute;
    left: 0;
    bottom: 0;
    right: 0;
    height: 1px;
    border-bottom: 1rpx solid rgba(0, 0, 0, 0.1);
    color: rgba(0, 0, 0, 0.1);
}
.weui-actionsheet__title .weui-actionsheet__title-text {
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 2;
}
.weui-actionsheet__menu {
    color: rgba(0, 0, 0, 0.9);
    background-color: #ffffff;
}
.weui-actionsheet__action {
    margin-top: 8px;
    background-color: #ffffff;
    padding-bottom: constant(safe-area-inset-bottom);
    padding-bottom: env(safe-area-inset-bottom);
}
.weui-actionsheet__cell {
    position: relative;
    padding: 16px;
    text-align: center;
    font-size: 17px;
    line-height: 1.41176471;
}
.weui-actionsheet__cell:before {
    content: ' ';
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    height: 1px;
    border-top: 1rpx solid rgba(0, 0, 0, 0.1);
    color: rgba(0, 0, 0, 0.1);
}
.weui-actionsheet__cell:active {
    background-color: #ececec;
}
.weui-actionsheet__cell:first-child:before {
    display: none;
}
.weui-actionsheet__cell_warn {
    color: #fa5151;
}
.weui-skin_android .weui-actionsheet {
    position: fixed;
    left: 50%;
    top: 50%;
    bottom: auto;
    transform: translate(-50%, -50%);
    width: 274px;
    box-sizing: border-box;
    backface-visibility: hidden;
    background: transparent;
    transition: transform 0.3s;
    border-radius: 2px;
}
.weui-skin_android .weui-actionsheet__action {
    display: none;
}
.weui-skin_android .weui-actionsheet__menu {
    border-radius: 2px;
    box-shadow: 0 6px 30px 0 rgba(0, 0, 0, 0.1);
}
.weui-skin_android .weui-actionsheet__cell {
    padding: 16px;
    font-size: 17px;
    line-height: 1.41176471;
    color: rgba(0, 0, 0, 0.9);
    text-align: left;
}
.weui-skin_android .weui-actionsheet__cell:first-child {
    border-top-left-radius: 2px;
    border-top-right-radius: 2px;
}
.weui-skin_android .weui-actionsheet__cell:last-child {
    border-bottom-left-radius: 2px;
    border-bottom-right-radius: 2px;
}
.weui-actionsheet_toggle {
    transform: translate(0, 0);
}
.weui-mask.weui-mask_hidden {
    opacity: 0;
    transform: scale3d(1, 1, 0);
}
.weui-mask {
    opacity: 1;
    transform: scale3d(1, 1, 1);
    transition: all 0.3s;
}
</style>
