<template>
    <view class="page" :style="'height:' + height + 'px'">
        <view class="title">环信直播条约</view>
        <view class="content">
            <view class="margin-top-10">
                为了抵制一切不良信息的传播，净化直播内容，给观众最好的直播体验，建立健康的直播生态环境，环信直播要求主播严格遵守《环信直播直播公约》，共同营造健康和谐的直播环境。
            </view>

            <view class="margin-top-10">1、禁止以任何形式播放包含危害国家安全、泄露国家秘密、颠覆国家政权、破坏国家统一的相关内容；</view>

            <view class="margin-top-10">2、禁止以任何形式播放包含破坏民族团结、损害国家荣誉和利益、煽动民族仇恨、民族歧视、破坏民族团结的相关内容；</view>

            <view class="margin-top-10">3、禁止以任何形式播放包含破坏国家宗教政策、宣扬邪教和封建迷信的相关内容；</view>

            <view class="margin-top-10">4、禁止以任何形式播放包含淫秽、色情、赌博、暴力、凶杀、恐怖或者教唆犯罪的相关内容；</view>

            <view class="margin-top-10">
                5、禁止以任何形式播放国家相关法律、行政法规明令禁止的内容或其他另人反感的包括但不限于资讯、资料、文字、软件、音乐、照片、图形、信息或其他资料；
            </view>

            <view class="margin-top-10">6、禁止播放转播未获得授权的体育比赛有关内容；</view>

            <view class="margin-top-10">7、禁止游戏直播时，出现影响游戏内玩家内体验等行为；</view>

            <view class="margin-top-10">8、禁止发放虚假广告、兼职广告；</view>

            <view class="margin-top-10">9、禁止播放版权所有人禁止播放的视频作品；</view>

            <view class="margin-top-10">10、禁止播放其他经过工作人员提醒的内容。</view>

            <view class="margin-top-10" style="margin-bottom: 20rpx">
                若环信直播在审核直播时发现有任何违反《环信直播直播公约》的行为，环信直播将在第一时间查封该账号，并提出警告。若情节恶劣，环信直播将会把其身份证、手机信息拉入黑名单，永久停止对该身份证件、手机信息用户开放注册服务。若情节严重触犯中华人民共和国刑法，环信直播将会把该用户相关信息提交国家公安机关，协助公安机关追究该用户的法律责任。
            </view>
        </view>
    </view>
</template>

<script>
export default {
    data() {
        return {
            height: 0
        };
    },
    onLoad() {
        let self = this;
        uni.getSystemInfo({
            success: function (res) {
                self.setData({
                    height: res.windowHeight
                });
            }
        });
    },
    methods: {}
};
</script>
<style>
.page::before {
    content: '';
    display: table;
}

.page {
    background-color: #f9f9f9;
    font-size: 28rpx;
}

.title {
    width: 96%;
    margin: 20rpx auto 0;
    padding-bottom: 20rpx;
    border-bottom: 1px solid #e6e6e6;
    font-size: 32rpx;
    font-weight: 600;
}

.content {
    width: 96%;
    margin: auto;
    text-align: justify;
}

.margin-top-10 {
    margin-top: 20rpx;
}
</style>
