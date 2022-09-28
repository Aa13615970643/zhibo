<template>
    <view>
        <view :class="'weui-uploader ' + extClass">
            <view class="weui-uploader__hd">
                <div class="weui-uploader__overview">
                    <view class="weui-uploader__title">{{ title }}</view>
                    <view class="weui-uploader__info" v-if="maxCount > 1">{{ currentFiles.length }}/{{ maxCount }}</view>
                </div>
                <view v-if="tips" class="weui-uploader__tips">{{ tips }}</view>
                <view v-else><slot name="tips"></slot></view>
            </view>
            <view class="weui-uploader__bd">
                <view class="weui-uploader__files">
                    <block v-for="(item, index) in currentFiles" :key="index">
                        <view v-if="item.error" :data-index="index" @tap="previewImage" class="weui-uploader__file weui-uploader__file_status">
                            <image class="weui-uploader__img" :src="item.url" mode="aspectFill" />
                            <view class="weui-uploader__file-content">
                                <icon type="warn" size="23" color="#F43530"></icon>
                            </view>
                        </view>

                        <view v-else-if="item.loading" :data-index="index" @tap="previewImage" class="weui-uploader__file weui-uploader__file_status">
                            <image class="weui-uploader__img" :src="item.url" mode="aspectFill" />
                            <view class="weui-uploader__file-content">
                                <view class="weui-loading"></view>
                            </view>
                        </view>

                        <view v-else class="weui-uploader__file" :data-index="index" @tap="previewImage">
                            <image class="weui-uploader__img" :src="item.url" mode="aspectFill" />
                        </view>
                    </block>
                </view>
                <view v-if="currentFiles.length < maxCount" class="weui-uploader__input-box">
                    <view class="weui-uploader__input" @tap="chooseImage"></view>
                </view>
            </view>
        </view>
        <mp-gallery :hide-on-click="true" :show-delete="showDelete" :show="showPreview" @delete="deletePic" :img-urls="previewImageUrls" :current="previewCurrent"></mp-gallery>
    </view>
</template>

<script>
export default {
    data() {
        return {
            currentFiles: [],
            showPreview: false,
            previewImageUrls: [],
            previewCurrent: '',
            filesClone: []
        };
    },
    options: {
        addGlobalClass: true
    },
    props: {
        title: {
            type: String,
            default: '图片上传'
        },
        sizeType: {
            type: Array,
            default: () => ['original', 'compressed']
        },
        sourceType: {
            type: Array,
            default: () => ['album', 'camera']
        },
        maxSize: {
            type: Number,
            default: 5 * 1024 * 1024
        },
        maxCount: {
            type: Number,
            default: 1
        },
        files: {
            type: Array,
            default: () => []
        },
        select: {
            type: Function,
            default: function value() {}
        },
        upload: {
            type: Function,
            default: null
        },
        tips: {
            type: String,
            default: ''
        },
        extClass: {
            type: String,
            default: ''
        },
        showDelete: {
            type: Boolean,
            default: true
        }
    },
    mounted: function ready() {},
    methods: {
        previewImage: function previewImage(e) {
            var index = e.currentTarget.dataset.index;
            var previewImageUrls = [];
            this.files.map(function (item) {
                previewImageUrls.push(item.url);
            });
            this.setData({
                previewImageUrls: previewImageUrls,
                previewCurrent: index,
                showPreview: true
            });
        },
        chooseImage: function chooseImage(e) {
            var that = this;

            if (this.uploading) {
                return;
            }

            uni.chooseImage({
                count: this.maxCount - this.files.length,
                success: function success(res) {
                    var invalidIndex = -1;
                    res.tempFiles.forEach(function (item, index) {
                        if (item.size > that.maxSize) {
                            invalidIndex = index;
                        }
                    });

                    if (typeof that.select === 'function') {
                        var ret = that.select(res);

                        if (ret === false) {
                            return;
                        }
                    }

                    if (invalidIndex >= 0) {
                        that.$emit(
                            'fail',
                            {
                                detail: {
                                    type: 1,
                                    errMsg: 'chooseImage:fail size exceed ' + that.maxSize,
                                    total: res.tempFilePaths.length,
                                    index: invalidIndex
                                }
                            },
                            {}
                        );
                        return;
                    }

                    var mgr = uni.getFileSystemManager();
                    var contents = res.tempFilePaths.map(function (item) {
                        var fileContent = mgr.readFileSync(item);
                        return fileContent;
                    });
                    var obj = {
                        tempFilePaths: res.tempFilePaths,
                        tempFiles: res.tempFiles,
                        contents: contents
                    };
                    that.$emit(
                        'select',
                        {
                            detail: obj
                        },
                        {}
                    );
                    var files = res.tempFilePaths.map(function (item, i) {
                        return {
                            loading: true,
                            url: 'data:image/jpg;base64,' + uni.arrayBufferToBase64(contents[i])
                        };
                    });

                    if (!files || !files.length) {
                        return;
                    }

                    if (typeof that.upload === 'function') {
                        var len = that.files.length;
                        var newFiles = that.files.concat(files);
                        that.setData({
                            filesClone: newFiles,
                            currentFiles: newFiles
                        });
                        that.loading = true;
                        that.upload(obj)
                            .then(function (json) {
                                that.loading = false;

                                if (json.urls) {
                                    var oldFiles = that.files;
                                    json.urls.forEach(function (url, index) {
                                        oldFiles[len + index].url = url;
                                        oldFiles[len + index].loading = false;
                                    });
                                    that.setData({
                                        filesClone: oldFiles,
                                        currentFiles: newFiles
                                    });
                                    that.$emit(
                                        'success',
                                        {
                                            detail: json
                                        },
                                        {}
                                    );
                                } else {
                                    that.$emit(
                                        'fail',
                                        {
                                            detail: {
                                                type: 3,
                                                errMsg: 'upload file fail, urls not found'
                                            }
                                        },
                                        {}
                                    );
                                }
                            })
                            .catch(function (err) {
                                that.loading = false;
                                var oldFiles = that.files;
                                res.tempFilePaths.map(function (item, index) {
                                    oldFiles[len + index].error = true;
                                    oldFiles[len + index].loading = false;
                                });
                                that.setData({
                                    filesClone: oldFiles,
                                    currentFiles: newFiles
                                });
                                that.$emit(
                                    'fail',
                                    {
                                        detail: {
                                            type: 3,
                                            errMsg: 'upload file fail',
                                            error: err
                                        }
                                    },
                                    {}
                                );
                            });
                    }
                },
                fail: function fail(_fail) {
                    if (_fail.errMsg.indexOf('chooseImage:fail cancel') >= 0) {
                        that.$emit(
                            'cancel',
                            {
                                detail: {}
                            },
                            {}
                        );
                        return;
                    }

                    _fail.type = 2;
                    that.$emit(
                        'fail',
                        {
                            detail: _fail
                        },
                        {}
                    );
                }
            });
        },
        deletePic: function deletePic(e) {
            var index = e.detail.index;
            var files = this.files;
            var file = files.splice(index, 1);
            this.setData({
                filesClone: files,
                currentFiles: files
            });
            this.$emit('delete', {
                detail: {
                    index: index,
                    item: file[0]
                }
            });
        }
    },
    watch: {
        files: {
            handler: function observer(newVal, oldVal, changedP) {
                this.filesClone = this.deepClone(this.files);
                this.setData({
                    currentFiles: newVal
                });
            },

            immediate: true,
            deep: true
        }
    }
};
/***/
</script>
<style>
.weui-uploader {
    flex: 1;
}
.weui-uploader__hd {
    padding-bottom: 16px;
}
.weui-uploader__overview {
    display: flex;
    align-items: center;
}
.weui-uploader__title {
    flex: 1;
}
.weui-uploader__tips {
    color: rgba(0, 0, 0, 0.3);
    font-size: 14px;
    line-height: 1.4;
    padding-top: 4px;
}
.weui-uploader__info {
    color: rgba(0, 0, 0, 0.3);
}
.weui-uploader__bd {
    margin-bottom: -8px;
    margin-right: -8px;
    overflow: hidden;
}
.weui-uploader__file {
    float: left;
    margin-right: 8px;
    margin-bottom: 8px;
}
.weui-uploader__img {
    display: block;
    width: 96px;
    height: 96px;
}
.weui-uploader__file_status {
    position: relative;
}
.weui-uploader__file_status:before {
    content: ' ';
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background-color: rgba(0, 0, 0, 0.5);
}
.weui-uploader__file-content {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    color: #ffffff;
}
.weui-uploader__input-box {
    float: left;
    position: relative;
    margin-right: 8px;
    margin-bottom: 8px;
    width: 96px;
    height: 96px;
    box-sizing: border-box;
    background-color: #ededed;
}
.weui-uploader__input-box:before,
.weui-uploader__input-box:after {
    content: ' ';
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: #a3a3a3;
}
.weui-uploader__input-box:before {
    width: 2px;
    height: 32px;
}
.weui-uploader__input-box:after {
    width: 32px;
    height: 2px;
}
.weui-uploader__input-box:active {
    border-color: #8b8b8b;
}
.weui-uploader__input-box:active:before,
.weui-uploader__input-box:active:after {
    background-color: #8b8b8b;
}
.weui-uploader__input {
    position: absolute;
    z-index: 1;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    opacity: 0;
}
.weui-loading {
    margin: 0 5px;
    width: 20px;
    height: 20px;
    display: inline-block;
    vertical-align: middle;
    animation: weuiLoading 1s steps(12, end) infinite;
    background: transparent
        url(data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIxMjAiIGhlaWdodD0iMTIwIiB2aWV3Qm94PSIwIDAgMTAwIDEwMCI+PHBhdGggZmlsbD0ibm9uZSIgZD0iTTAgMGgxMDB2MTAwSDB6Ii8+PHJlY3Qgd2lkdGg9IjciIGhlaWdodD0iMjAiIHg9IjQ2LjUiIHk9IjQwIiBmaWxsPSIjRTlFOUU5IiByeD0iNSIgcnk9IjUiIHRyYW5zZm9ybT0idHJhbnNsYXRlKDAgLTMwKSIvPjxyZWN0IHdpZHRoPSI3IiBoZWlnaHQ9IjIwIiB4PSI0Ni41IiB5PSI0MCIgZmlsbD0iIzk4OTY5NyIgcng9IjUiIHJ5PSI1IiB0cmFuc2Zvcm09InJvdGF0ZSgzMCAxMDUuOTggNjUpIi8+PHJlY3Qgd2lkdGg9IjciIGhlaWdodD0iMjAiIHg9IjQ2LjUiIHk9IjQwIiBmaWxsPSIjOUI5OTlBIiByeD0iNSIgcnk9IjUiIHRyYW5zZm9ybT0icm90YXRlKDYwIDc1Ljk4IDY1KSIvPjxyZWN0IHdpZHRoPSI3IiBoZWlnaHQ9IjIwIiB4PSI0Ni41IiB5PSI0MCIgZmlsbD0iI0EzQTFBMiIgcng9IjUiIHJ5PSI1IiB0cmFuc2Zvcm09InJvdGF0ZSg5MCA2NSA2NSkiLz48cmVjdCB3aWR0aD0iNyIgaGVpZ2h0PSIyMCIgeD0iNDYuNSIgeT0iNDAiIGZpbGw9IiNBQkE5QUEiIHJ4PSI1IiByeT0iNSIgdHJhbnNmb3JtPSJyb3RhdGUoMTIwIDU4LjY2IDY1KSIvPjxyZWN0IHdpZHRoPSI3IiBoZWlnaHQ9IjIwIiB4PSI0Ni41IiB5PSI0MCIgZmlsbD0iI0IyQjJCMiIgcng9IjUiIHJ5PSI1IiB0cmFuc2Zvcm09InJvdGF0ZSgxNTAgNTQuMDIgNjUpIi8+PHJlY3Qgd2lkdGg9IjciIGhlaWdodD0iMjAiIHg9IjQ2LjUiIHk9IjQwIiBmaWxsPSIjQkFCOEI5IiByeD0iNSIgcnk9IjUiIHRyYW5zZm9ybT0icm90YXRlKDE4MCA1MCA2NSkiLz48cmVjdCB3aWR0aD0iNyIgaGVpZ2h0PSIyMCIgeD0iNDYuNSIgeT0iNDAiIGZpbGw9IiNDMkMwQzEiIHJ4PSI1IiByeT0iNSIgdHJhbnNmb3JtPSJyb3RhdGUoLTE1MCA0NS45OCA2NSkiLz48cmVjdCB3aWR0aD0iNyIgaGVpZ2h0PSIyMCIgeD0iNDYuNSIgeT0iNDAiIGZpbGw9IiNDQkNCQ0IiIHJ4PSI1IiByeT0iNSIgdHJhbnNmb3JtPSJyb3RhdGUoLTEyMCA0MS4zNCA2NSkiLz48cmVjdCB3aWR0aD0iNyIgaGVpZ2h0PSIyMCIgeD0iNDYuNSIgeT0iNDAiIGZpbGw9IiNEMkQyRDIiIHJ4PSI1IiByeT0iNSIgdHJhbnNmb3JtPSJyb3RhdGUoLTkwIDM1IDY1KSIvPjxyZWN0IHdpZHRoPSI3IiBoZWlnaHQ9IjIwIiB4PSI0Ni41IiB5PSI0MCIgZmlsbD0iI0RBREFEQSIgcng9IjUiIHJ5PSI1IiB0cmFuc2Zvcm09InJvdGF0ZSgtNjAgMjQuMDIgNjUpIi8+PHJlY3Qgd2lkdGg9IjciIGhlaWdodD0iMjAiIHg9IjQ2LjUiIHk9IjQwIiBmaWxsPSIjRTJFMkUyIiByeD0iNSIgcnk9IjUiIHRyYW5zZm9ybT0icm90YXRlKC0zMCAtNS45OCA2NSkiLz48L3N2Zz4=)
        no-repeat;
    background-size: 100%;
}
.weui-loading.weui-loading_transparent {
    background-image: url("data:image/svg+xml;charset=utf-8,%3Csvg xmlns='http://www.w3.org/2000/svg' width='120' height='120' viewBox='0 0 100 100'%3E%3Cpath fill='none' d='M0 0h100v100H0z'/%3E%3Crect xmlns='http://www.w3.org/2000/svg' width='7' height='20' x='46.5' y='40' fill='rgba(255,255,255,.56)' rx='5' ry='5' transform='translate(0 -30)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='rgba(255,255,255,.5)' rx='5' ry='5' transform='rotate(30 105.98 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='rgba(255,255,255,.43)' rx='5' ry='5' transform='rotate(60 75.98 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='rgba(255,255,255,.38)' rx='5' ry='5' transform='rotate(90 65 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='rgba(255,255,255,.32)' rx='5' ry='5' transform='rotate(120 58.66 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='rgba(255,255,255,.28)' rx='5' ry='5' transform='rotate(150 54.02 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='rgba(255,255,255,.25)' rx='5' ry='5' transform='rotate(180 50 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='rgba(255,255,255,.2)' rx='5' ry='5' transform='rotate(-150 45.98 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='rgba(255,255,255,.17)' rx='5' ry='5' transform='rotate(-120 41.34 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='rgba(255,255,255,.14)' rx='5' ry='5' transform='rotate(-90 35 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='rgba(255,255,255,.1)' rx='5' ry='5' transform='rotate(-60 24.02 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='rgba(255,255,255,.03)' rx='5' ry='5' transform='rotate(-30 -5.98 65)'/%3E%3C/svg%3E");
}
@keyframes weuiLoading {
    0% {
        transform: rotate3d(0, 0, 1, 0deg);
    }
    100% {
        transform: rotate3d(0, 0, 1, 360deg);
    }
}
</style>
