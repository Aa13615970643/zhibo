<template>
    <view>
        <checkbox-group :class="extClass" v-if="multi" @change="checkedChange">
            <slot></slot>
        </checkbox-group>
        <radio-group :class="extClass" wx-else @change="checkedChange">
            <slot></slot>
        </radio-group>
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
            targetList: [],
            parentCell: null,
            checked: false
        };
    },
    props: {
        multi: {
            type: Boolean,
            default: true
        },
        extClass: {
            type: String,
            default: ''
        },
        prop: {
            type: String,
            default: ''
        }
    },
    relations: {
        '../checkbox/checkbox': {
            type: 'descendant',
            linked: function linked(target) {
                this.targetList.push(target);
                target.setMulti(this.multi);

                if (!this.firstItem) {
                    this.firstItem = target;
                }

                if (target !== this.firstItem) {
                    target.setOuterClass('weui-cell_wxss');
                }
            },
            unlinked: function unlinked(target) {
                var index = -1;
                this.targetList.forEach(function (item, idx) {
                    if (item === target) {
                        index = idx;
                    }
                });
                this.targetList.splice(index, 1);

                if (!this.targetList) {
                    this.firstItem = null;
                }
            }
        },
        '../form/form': {
            type: 'ancestor'
        },
        '../cells/cells': {
            type: 'ancestor',
            linked: function linked(target) {
                if (!this.parentCell) {
                    this.parentCell = target;
                }

                this.setParentCellsClass();
            },
            unlinked: function unlinked(target) {
                this.parentCell = null;
            }
        }
    },
    methods: {
        checkedChange: function checkedChange(checked, target) {
            console.log('checked change', checked);

            if (this.multi) {
                var vals = [];
                this.targetList.forEach(function (item) {
                    if (item.data.checked) {
                        vals.push(item.data.value);
                    }
                });
                this.$emit('change', {
                    detail: {
                        value: vals
                    }
                });
            } else {
                var val = '';
                this.targetList.forEach(function (item) {
                    if (item === target) {
                        val = item.data.value;
                    } else {
                        item.setData({
                            checked: false
                        });
                    }
                });
                this.$emit(
                    'change',
                    {
                        detail: {
                            value: val
                        }
                    },
                    {}
                );
            }
        },
        setParentCellsClass: function setParentCellsClass() {
            var className = this.multi ? 'weui-cells_checkbox' : '';

            if (this.parentCell) {
                this.parentCell.setCellsClass(className);
            }
        },
        _multiChange: function _multiChange(multi) {
            this.targetList.forEach(function (target) {
                target.setMulti(multi);
            });

            if (this.parentCell) {
                this.parentCell.setCellMulti(multi);
            }

            return multi;
        }
    },
    watch: {
        multi: {
            handler: function _multiChange(multi) {
                this.targetList.forEach(function (target) {
                    target.setMulti(multi);
                });

                if (this.parentCell) {
                    this.parentCell.setCellMulti(multi);
                }

                return multi;
            },

            immediate: true
        }
    }
};
/***/
</script>
<style></style>
