<template>
    <view>
        <mp-cell
            :has-footer="!multiClone"
            :has-header="multiClone"
            @tap.native="checkedChange"
            :footer-class="!multiClone ? 'weui-check__ft_in-radio' : ''"
            :icon-class="multiClone ? 'weui-check__hd_in-checkbox' : ''"
            :ext-class="'weui-check__label ' + outerClass + ' ' + extClass + ' ' + (!multiClone ? 'weui-cell_radio' : 'weui-cell_checkbox')"
        >
            <view slot="icon" v-if="multiClone">
                <checkbox :value="value" :checked="checkedClone" :disabled="disabled" :color="color" class="weui-check"></checkbox>
                <!-- 未勾选 -->
                <icon v-if="!checkedClone" size="23" class="weui-icon-checkbox_circle" type="circle"></icon>
                <icon v-else size="23" class="weui-icon-checkbox_success" type="success"></icon>
            </view>
            <view>{{ label }}</view>
            <view slot="footer" v-if="!multiClone">
                <radio :value="value" :checked="checkedClone" :disabled="disabled" :color="color" class="weui-check"></radio>
                <!-- 已勾选 -->
                <icon size="16" v-if="checkedClone" class="weui-icon-radio" type="success_no_circle"></icon>
            </view>
        </mp-cell>
    </view>
</template>

<script>
import mpCell from '../cell/cell';
import mpCheckboxGroup from '../checkbox-group/checkbox-group';

export default {
    components: {
        mpCell,
        mpCheckboxGroup
    },
    data() {
        return {
            outerClass: '',
            disabled: '',
            color: '',
            multiClone: '',
            checkedClone: ''
        };
    },
    options: {
        addGlobalClass: true,
        multipleSlots: true
    },
    props: {
        multi: {
            type: Boolean,
            default: true
        },
        checked: {
            type: Boolean,
            default: false
        },
        value: {
            type: String,
            default: ''
        },
        label: {
            type: String,
            default: 'label'
        },
        extClass: {
            type: String,
            default: ''
        }
    },
    relations: {
        '../checkbox-group/checkbox-group': {
            type: 'ancestor',
            linked: function linked(target) {
                this.group = target;
            },
            unlinked: function unlinked() {
                this.group = null;
            }
        }
    },
    methods: {
        setMulti: function setMulti(multi) {
            this.setData({
                multiClone: multi
            });
        },
        setOuterClass: function setOuterClass(className) {
            this.setData({
                outerClass: className
            });
        },
        checkedChange: function checkedChange(e) {
            if (this.multi) {
                var checked = !this.checked;
                this.setData({
                    checkedClone: checked
                });

                if (this.group) {
                    this.group.checkedChange(checked, this);
                }
            } else {
                var _checked = this.checked;

                if (_checked) {
                    return;
                }

                this.setData({
                    checkedClone: true
                });

                if (this.group) {
                    this.group.checkedChange(_checked, this);
                }
            }

            this.$emit('change', {
                detail: {
                    value: this.value,
                    checked: this.checked
                }
            });
        }
    },
    watch: {
        multi: {
            handler: function (newVal, oldVal) {
                this.multiClone = newVal;
            },

            immediate: true
        },

        checked: {
            handler: function (newVal, oldVal) {
                this.checkedClone = newVal;
            },

            immediate: true
        }
    }
};
/***/
</script>
<style>
.weui-cells_checkbox .weui-check__label:before {
    left: 55px;
}
.weui-check__label:active {
    background-color: #ececec;
}
.weui-check {
    position: absolute;
    left: -9999px;
}
.weui-check__hd_in-checkbox {
    padding-right: 16px;
}
.weui-cell__ft_in-radio {
    padding-left: 16px;
}
</style>
