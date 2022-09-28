<template>
    <view>
        <block v-if="link">
            <view
                @tap="navigateTo"
                :class="'weui-cell weui-cell_access ' + extClass + ' ' + outerClass + (inForm ? ' weui-cell-inform' : '') + (inline ? '' : ' .weui-cell_label-block')"
                :hover-class="hover ? 'weui-cell_active' : ''"
            >
                <view v-if="hasHeader" :class="'weui-cell__hd ' + iconClass">
                    <block v-if="icon">
                        <image :src="icon" class="weui-cell__icon" mode="aspectFit"></image>
                    </block>
                    <block v-else>
                        <slot name="icon"></slot>
                    </block>
                    <block v-if="inForm">
                        <block v-if="title">
                            <view class="weui-label">{{ title }}</view>
                        </block>
                        <block v-else>
                            <slot name="title"></slot>
                        </block>
                    </block>
                    <block v-else>
                        <block v-if="title">{{ title }}</block>
                        <block v-else>
                            <slot name="title"></slot>
                        </block>
                    </block>
                </view>
                <view v-if="hasBody" class="weui-cell__bd">
                    <block v-if="value">{{ value }}</block>
                    <block v-else>
                        <slot></slot>
                    </block>
                </view>
                <view v-if="hasFooter" :class="'weui-cell__ft weui-cell__ft_in-access ' + footerClass">
                    <block v-if="footer">{{ footer }}</block>
                    <block v-else>
                        <slot name="footer"></slot>
                    </block>
                </view>
            </view>
        </block>
        <block v-else>
            <view
                @tap="navigateTo"
                :class="'weui-cell ' + (showError && error ? 'weui-cell_warn' : '') + ' ' + (inForm ? 'weui-cell-inform' : '') + ' ' + extClass + ' ' + outerClass"
                :hover-class="hover ? 'weui-cell_active' : ''"
            >
                <view v-if="hasHeader" :class="'weui-cell__hd ' + iconClass">
                    <block v-if="icon">
                        <image :src="icon" class="weui-cell__icon" mode="aspectFit"></image>
                    </block>
                    <block v-else>
                        <slot name="icon"></slot>
                    </block>
                    <block v-if="inForm">
                        <block v-if="title">
                            <view class="weui-label">{{ title }}</view>
                        </block>
                        <block v-else>
                            <slot name="title"></slot>
                        </block>
                    </block>
                    <block v-else>
                        <block v-if="title">{{ title }}</block>
                        <block v-else>
                            <slot name="title"></slot>
                        </block>
                    </block>
                </view>
                <view v-if="hasBody" :class="'weui-cell__bd ' + bodyClass">
                    <block v-if="value">{{ value }}</block>
                    <block v-else>
                        <slot></slot>
                    </block>
                </view>
                <view v-if="hasFooter" :class="'weui-cell__ft ' + footerClass">
                    <block v-if="footer">{{ footer }}</block>
                    <block v-else>
                        <slot name="footer"></slot>
                    </block>
                    <icon v-if="showError && error" type="warn" size="23" color="#E64340"></icon>
                </view>
            </view>
        </block>
    </view>
</template>

<script>
import mpCells from '../cells/cells';

export default {
    components: {
        mpCells
    },
    data() {
        return {
            inForm: false,
            error: '',
            outerClass: ''
        };
    },
    options: {
        addGlobalClass: true,
        multipleSlots: true
    },
    props: {
        hover: {
            type: Boolean,
            default: false
        },
        link: {
            type: Boolean,
            default: false
        },
        extClass: {
            type: String,
            default: ''
        },
        iconClass: {
            type: String,
            default: ''
        },
        bodyClass: {
            type: String,
            default: ''
        },
        icon: {
            type: String,
            default: ''
        },
        title: {
            type: String,
            default: ''
        },
        value: {
            type: String,
            default: ''
        },
        showError: {
            type: Boolean,
            default: false
        },
        prop: {
            type: String,
            default: ''
        },
        url: {
            type: String,
            default: ''
        },
        footerClass: {
            type: String,
            default: ''
        },
        footer: {
            type: String,
            default: ''
        },
        inline: {
            type: Boolean,
            default: true
        },
        hasHeader: {
            type: Boolean,
            default: true
        },
        hasFooter: {
            type: Boolean,
            default: true
        },
        hasBody: {
            type: Boolean,
            default: true
        }
    },
    relations: {
        '../form/form': {
            type: 'ancestor'
        },
        '../cells/cells': {
            type: 'ancestor'
        }
    },
    methods: {
        setError: function setError(error) {
            this.setData({
                error: error || false
            });
        },
        setInForm: function setInForm() {
            this.setData({
                inForm: true
            });
        },
        setOuterClass: function setOuterClass(className) {
            this.setData({
                outerClass: className
            });
        },
        navigateTo: function navigateTo() {
            var that = this;
            var data = this;

            if (data.url && data.link) {
                uni.navigateTo({
                    url: data.url,
                    success: function success(res) {
                        that.$emit(
                            'navigatesuccess',
                            {
                                detail: res
                            },
                            {}
                        );
                    },
                    fail: function fail(_fail) {
                        that.$emit(
                            'navigateerror',
                            {
                                detail: _fail
                            },
                            {}
                        );
                    }
                });
            }
        }
    }
};
/***/
</script>
<style>
.weui-cells {
    position: relative;
    margin-top: 8px;
    background-color: #ffffff;
    line-height: 1.41176471;
    font-size: 17px;
}
.weui-cells:before {
    content: ' ';
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    height: 1px;
    border-top: 1rpx solid rgba(0, 0, 0, 0.1);
    color: rgba(0, 0, 0, 0.1);
}
.weui-cells:after {
    content: ' ';
    position: absolute;
    left: 0;
    bottom: 0;
    right: 0;
    height: 1px;
    border-bottom: 1rpx solid rgba(0, 0, 0, 0.1);
    color: rgba(0, 0, 0, 0.1);
}
.weui-cells__title {
    margin-top: 16px;
    margin-bottom: 3px;
    padding-left: 16px;
    padding-right: 16px;
    color: rgba(0, 0, 0, 0.5);
    font-size: 14px;
}
.weui-cells_after-title {
    margin-top: 0;
}
.weui-cells__tips {
    margin-top: 3px;
    color: rgba(0, 0, 0, 0.5);
    padding-left: 16px;
    padding-right: 16px;
    font-size: 14px;
}
.weui-cell {
    padding: 16px;
    position: relative;
    display: flex;
    align-items: center;
}
.weui-cell:before {
    content: ' ';
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    height: 1px;
    border-top: 1rpx solid rgba(0, 0, 0, 0.1);
    color: rgba(0, 0, 0, 0.1);
    left: 16px;
}
.weui-cell:first-child:before {
    display: none;
}
.weui-cell_active {
    background-color: #ececec;
}
.weui-cell_primary {
    align-items: flex-start;
}
.weui-cell__bd {
    flex: 1;
}
.weui-cell__ft {
    text-align: right;
    color: rgba(0, 0, 0, 0.5);
}
.weui-cell_wxss.weui-cell_wxss:before {
    display: block;
}
.weui-cell_label-block {
    display: block;
}
.weui-cell_label-block .weui-label {
    width: auto;
    word-break: initial;
    -webkit-hyphens: auto;
    hyphens: auto;
}
.weui-cell_vcode {
    padding-top: 0;
    padding-right: 0;
    padding-bottom: 0;
}
.weui-vcode-img {
    margin-left: 5px;
    height: 3.29411765em;
    vertical-align: middle;
}
.weui-vcode-btn {
    display: inline-block;
    height: 3.29411765em;
    margin-left: 5px;
    padding: 0 0.6em 0 0.7em;
    border-left: 1rpx solid rgba(0, 0, 0, 0.1);
    line-height: 3.29411765em;
    vertical-align: middle;
    font-size: 17px;
    color: #576b95;
    white-space: nowrap;
}
button.weui-vcode-btn {
    min-height: 0;
    background-color: transparent;
    border: 0;
    outline: 0;
}
.weui-vcode-btn:active {
    color: #767676;
}
</style>
