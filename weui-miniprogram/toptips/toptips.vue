<template>
    <view :class="'weui-toptips ' + className + ' ' + extClass + ' ' + (showClone ? 'weui-toptips_show' : '')">
        <block v-if="msg">{{ msg }}</block>
        <block v-else>
            <slot></slot>
        </block>
    </view>
</template>

<script>
export default {
    data() {
        return {
            typeClassMap: {
                warn: 'weui-toptips_warn',
                info: 'weui-toptips_info',
                success: 'weui-toptips_success',
                error: 'weui-toptips_error'
            },

            className: '',
            showClone: false
        };
    },
    options: {
        addGlobalClass: true
    },
    props: {
        type: {
            type: String,
            default: 'error'
        },
        show: {
            type: Boolean,
            default: false
        },
        msg: {
            type: String,
            default: ''
        },
        delay: {
            type: Number,
            default: 2000
        },
        extClass: {
            type: String,
            default: ''
        }
    },
    beforeMount: function attached() {
        var data = this;
        this.setData({
            className: data.typeClassMap[data.type] || ''
        });
    },
    methods: {
        _typeChange: function _typeChange(newVal) {
            this.setData({
                className: this.typeClassMap[newVal] || ''
            });
            return newVal;
        },
        _showChange: function _showChange(newVal) {
            this.showClone = this.deepClone(this.show);
            this._showToptips(newVal);
        },
        _showToptips: function _showToptips(newVal) {
            var that = this;

            if (newVal && this.delay) {
                setTimeout(function () {
                    that.setData(
                        {
                            showClone: false
                        },
                        function () {
                            that.$emit(
                                'hide',
                                {
                                    detail: {}
                                },
                                {}
                            );
                        }
                    );
                }, this.delay);
            }

            this.setData({
                showClone: newVal
            });
        }
    },
    watch: {
        type: {
            handler: function _typeChange(newVal) {
                this.setData({
                    className: this.typeClassMap[newVal] || ''
                });
                return newVal;
            },

            immediate: true
        },

        show: {
            handler: function _showChange(newVal) {
                this.showClone = this.deepClone(this.show);
                this._showToptips(newVal);
            },

            immediate: true
        }
    }
};
/***/
</script>
<style>
.weui-toptips {
    position: fixed;
    -webkit-transform: translateZ(0) translateY(calc(-100% - 8px));
    transform: translateZ(0) translateY(calc(-100% - 8px));
    text-align: center;
    top: 8px;
    left: 16px;
    right: 16px;
    border-radius: 4px;
    padding: 8px;
    -webkit-border-radius: 4px;
    color: rgba(255, 255, 255, 0.9);
    font-size: 17px;
    line-height: 1.4;
    background: rgba(250, 81, 81, 0.9);
    z-index: 5000;
    word-wrap: break-word;
    word-break: break-all;
    -webkit-transition: all 0.4s ease-in-out;
    transition: all 0.4s ease-in-out;
}
.weui-toptips_show {
    -webkit-transform: translateZ(0) translateY(0);
    transform: translateZ(0) translateY(0);
    opacity: 1;
}
.weui-toptips_warn {
    background-color: #fa5151;
}
.weui-toptips_success {
    background-color: #09bb07;
}
.weui-toptips_error {
    background-color: #fa5151;
}
.weui-toptips_info {
    background-color: #10aeff;
}
</style>
