<template>
    <view :class="'weui-badge ' + extClass + ' ' + (!content ? 'weui-badge_dot' : '')">{{ content }}</view>
</template>

<script>
export default {
    data() {
        return {};
    },
    options: {
        addGlobalClass: true
    },
    props: {
        extClass: {
            type: String,
            default: ''
        },
        content: {
            type: String,
            default: ''
        }
    }
};
/***/
</script>
<style>
.weui-badge {
    display: inline-block;
    padding: 0.15em 0.4em;
    min-width: 8px;
    border-radius: 18px;
    background-color: #fa5151;
    color: #ffffff;
    line-height: 1.2;
    text-align: center;
    font-size: 12px;
    vertical-align: middle;
}
.weui-badge_dot {
    padding: 0.4em;
    min-width: 0;
}
</style>
