<template>
    <view :class="extClass + ' weui-cells__group ' + outerClass + ' ' + childClass">
        <view v-if="title" class="weui-cells__title">{{ title }}</view>
        <view :class="'weui-cells weui-cells_after-title weui-cells_form ' + (checkboxCount > 0 && checkboxIsMulti ? 'weui-cells_checkbox' : '')">
            <slot></slot>
        </view>
        <view v-if="footer" class="weui-cells__tips">{{ footer }}</view>
        <slot name="footer" v-else></slot>
    </view>
</template>

<script>
export default {
    data() {
        return {
            firstItem: null,
            checkboxCount: 0,
            checkboxIsMulti: false,
            outerClass: '',
            childClass: ''
        };
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
        extClass: {
            type: String,
            default: ''
        },
        footer: {
            type: String,
            default: ''
        }
    },
    relations: {
        '../cell/cell': {
            type: 'descendant',
            linked: function linked(target) {
                if (!this.firstItem) {
                    this.firstItem = target;
                }

                if (target !== this.firstItem) {
                    target.setOuterClass('weui-cell_wxss');
                }
            }
        },
        '../form-page/form-page': {
            type: 'ancestor'
        },
        '../checkbox-group/checkbox-group': {
            type: 'descendant',
            linked: function linked(target) {
                this.setData({
                    checkboxCount: this.checkboxCount + 1,
                    checkboxIsMulti: target.data.multi
                });
            },
            unlinked: function unlinked(target) {
                this.setData({
                    checkboxCount: this.checkboxCount - 1,
                    checkboxIsMulti: target.data.multi
                });
            }
        }
    },
    methods: {
        setCellMulti: function setCellMulti(multi) {
            this.setData({
                checkboxIsMulti: multi
            });
        },
        setCellsClass: function setCellsClass(className) {
            this.setData({
                childClass: className
            });
        },
        setOuterClass: function setOuterClass(className) {
            this.setData({
                outerClass: className
            });
        }
    }
};
/***/
</script>
<style></style>
