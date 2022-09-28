<template>
    <view class="page" :style="'height:' + winheight + 'px'">
        <view class="report_block">
            <view
                :class="'report_block-label ' + (item.status ? 'report_block-label-color' : '')"
                :data-status="item.status"
                :data-index="index"
                @tap="reportLabelFun"
                :data-label="item.title"
                v-for="(item, index) in label"
                :key="item.title"
            >
                {{ item.title }}
            </view>
        </view>

        <form @submit="bindFormSubmit" class="advice-form">
            <textarea placeholder="请写下您的意见或者建议" name="textarea" :focus="true" class="advice" @input="reportContent" />
            <button form-type="submit" type="primary" @tap="commit" class="submit">提交</button>
        </form>
        <view class="advice-text">
            <text>小贴士</text>
            <text>请详细描述您举报该主播的原因,我们将第一时间处理</text>
            <text>
                工作时间:0:00-24:00
                <text>客服QQ：1439552119</text>
            </text>
        </view>
    </view>
</template>

<script>
export default {
    data() {
        return {
            picId: '',
            reportLabel: '',
            content: '',
            tel: '',

            label: [
                {
                    title: '盗版侵权',
                    status: false
                },
                {
                    title: '色情暴力',
                    status: false
                },
                {
                    title: '涉及政治',
                    status: false
                },
                {
                    title: '其他相关',
                    status: false
                }
            ],

            height: 0,
            winheight: ''
        };
    },
    onLoad: function (option) {
        let that = this;
        uni.getSystemInfo({
            success: function (res) {
                that.setData({
                    height: res.windowHeight
                });
            }
        });
    },
    methods: {
        reportLabelFun: function (e) {
            var index = e.currentTarget.dataset.index;
            var label = this.label;
            var title = e.currentTarget.dataset.label;
            var status = e.currentTarget.dataset.status;
            for (let i of label) {
                i.status = false;
            }

            if (status === false) {
                label[index].status = true;
                title = title;
            } else {
                label[index].status = false;
                title = '';
            }

            this.setData({
                label: label,
                reportLabel: title
            });
        },

        //举报原因
        reportContent: function (e) {
            var content = e.detail.value;
            this.setData({
                content: content
            });
        },

        //提交
        commit: function () {},

        bindFormSubmit() {
            console.log('占位：函数 bindFormSubmit 未声明');
        }
    }
};
</script>
<style>
.page::before {
    content: '';
    display: table-cell;
}

.report_block {
    height: 60px;
    width: 100%;
    padding: 20rpx;
    box-sizing: border-box;
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: 18rpx;
    background: #fff;
    color: #c1c1c1;
}

.report_block-label {
    padding: 6px 12px;
    border: 1px solid #e6e6e6;
    border-radius: 20px;
    font-size: 24rpx;
    color: #999;
    background: #fff;
}

.report_block-label-color {
    border: 1px solid #15b111;
}

.advice-form {
    font-size: 24rpx;
    color: #909090;
}

.advice {
    padding: 20rpx;
    width: 96%;
    margin: auto;
    height: 200rpx;
    background: #fff;
    font-size: 28rpx;
    color: #909090;
    box-sizing: border-box;
    border: 1px solid #e6e6e6;
    border-radius: 4rpx;
}

.advice-text {
    padding: 10rpx;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: flex-start;
    font-size: 24rpx;
    color: #909090;
}

.submit {
    width: 96%;
    margin: 20rpx auto;
    height: 80rpx;
    text-align: center;
    line-height: 80rpx;
    border-radius: 20px;
    color: #ffffff;
    background-color: rgb(28, 211, 155);
}
</style>
