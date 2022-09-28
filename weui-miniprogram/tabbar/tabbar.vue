<template>
    <view :class="'weui-tabbar ' + extClass">
        <!-- 选中的时候往weui-tabbar__item加class:weui-bar__item_on -->
        <view :data-index="index" @tap="tabChange" :class="'weui-tabbar__item ' + (index === currentClone ? 'weui-bar__item_on' : '')" v-for="(item, index) in list" :key="index">
            <view style="position: relative; display: inline-block">
                <image :src="currentClone === index ? item.selectedIconPath : item.iconPath" class="weui-tabbar__icon"></image>
                <mp-badge v-if="item.badge" :content="item.badge" style="position: absolute; top: -2px; left: calc(100% - 3px)"></mp-badge>
            </view>

            <view class="weui-tabbar__label">{{ item.text }}</view>
        </view>
    </view>
</template>

<script>
import mpBadge from '../badge/badge';

export default {
    data() {
        return {
            currentClone: ''
        };
    },
    components: {
        mpBadge
    },
    options: {
        addGlobalClass: true
    },
    props: {
        extClass: {
            type: String,
            default: ''
        },
        list: {
            type: Array,
            default: () => []
        },
        current: {
            type: Number,
            default: 0
        }
    },
    methods: {
        tabChange: function tabChange(e) {
            var index = e.currentTarget.dataset.index;

            if (index === this.current) {
                return;
            }

            this.setData({
                currentClone: index
            });
            this.$emit('change', {
                detail: {
                    index: index,
                    item: this.list[index]
                }
            });
        }
    },
    watch: {
        current: {
            handler: function (newVal, oldVal) {
                this.currentClone = newVal;
            },

            immediate: true
        }
    }
};
/***/
</script>
<style>
.weui-tabbar {
    display: flex;
    position: relative;
    z-index: 500;
    background-color: #f7f7f7;
}
.weui-tabbar:before {
    content: ' ';
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    height: 1px;
    border-top: 1rpx solid rgba(0, 0, 0, 0.1);
    color: rgba(0, 0, 0, 0.1);
}
.weui-tabbar__item {
    display: block;
    flex: 1;
    padding: 8px 0 4px;
    padding-bottom: calc(8px + constant(safe-area-inset-bottom));
    padding-bottom: calc(8px + env(safe-area-inset-bottom));
    font-size: 0;
    color: rgba(0, 0, 0, 0.5);
    text-align: center;
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}
.weui-tabbar__item:first-child {
    padding-left: constant(safe-area-inset-left);
    padding-left: env(safe-area-inset-left);
}
.weui-tabbar__item:last-child {
    padding-right: constant(safe-area-inset-right);
    padding-right: env(safe-area-inset-right);
}
.weui-tabbar__item.weui-bar__item_on .weui-tabbar__icon,
.weui-tabbar__item.weui-bar__item_on .weui-tabbar__icon > i,
.weui-tabbar__item.weui-bar__item_on .weui-tabbar__label {
    color: #07c160;
}
.weui-tabbar__icon {
    display: inline-block;
    width: 28px;
    height: 28px;
    margin-bottom: 2px;
}
i.weui-tabbar__icon,
.weui-tabbar__icon > i {
    font-size: 24px;
    color: rgba(0, 0, 0, 0.5);
}
.weui-tabbar__icon image {
    width: 100%;
    height: 100%;
}
.weui-tabbar__label {
    color: rgba(0, 0, 0, 0.9);
    font-size: 10px;
    line-height: 1.4;
}
</style>
