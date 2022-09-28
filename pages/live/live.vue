<template>
    <view>
        <view :style="'height: ' + height + 'px'">
            <!-- 播放区域 -->
            <image class="bg-img" :mode="widthFix" :src="src"></image>
            <!-- <view class="live-box">
    <video class="player" id="myVideo" src="http://wxsnsdy.tc.qq.com/105/20210/snsdyvideodownload?filekey=30280201010421301f0201690402534804102ca905ce620b1241b726bc41dcff44e00204012882540400&bizid=1023&hy=SH&fileparam=302c020101042530230204136ffd93020457e3c4ff02024ef202031e8d7f02030f42400204045a320a0201000400"
      danmu-list="{{danmuList}}" enable-danmu danmu-btn controls page-gesture></video>
  </view> -->
            <view>
                <!-- 房间顶部详情 -->
                <view>
                    <view class="navbar">
                        <view class="user-style" @tap="showHostDetail">
                            <image class="head-style" src="/static/images/live-logo.png" />
                            <text style="margin: 0 10px">{{ owner }}</text>
                        </view>
                        <!-- 礼物榜单列表（暂时写死） -->
                        <view class="user-right-style">
                            <image class="head-style" src="/static/images/head.png" />
                            <image class="head-style" src="/static/images/head.png" />
                            <image class="head-style" src="/static/images/head.png" />
                            <!-- 当前观看人数 -->
                            <view class="numPeople" @tap="changeMemberListModa">{{ audience }}人</view>
                        </view>
                    </view>
                </view>
                <view class="weui-tab__panel">
                    <view class="weui-tab__content">
                        <!-- 聊天 -->
                        <!-- 礼物提示 -->
                        <view class="promt" v-if="showGiftHint" :style="'opacity: ' + opacity">
                            <view class="lw-style">
                                <image class="head-style" src="/static/images/head.png" />
                                <view style="font-size: 10px">
                                    <text style="display: block">{{ nickName }}</text>
                                    <text style="opacity: 0.6">送出礼物</text>
                                </view>
                                <image class="lwImg" :src="giftModaData.url" />
                                <text style="font-size: 15px">x{{ giftNum }}</text>
                            </view>
                        </view>
                        <scroll-view :scroll-y="true" :style="'height:' + commentHeight + 'px;min-height:130px'" :scroll-into-view="toView">
                            <view class="comment-list" :id="'item' + item.id" v-for="(item, index) in msgList" :key="index">
                                <view v-if="item.type == 'txt' || item.contentsType == 'TEXT'">
                                    <text class="name">{{ item.nickName }}:</text>
                                    <text style="color: #ffc700">{{ item.msg || item.sourceMsg }}</text>
                                </view>

                                <view v-if="(item.contentsType == 'CUSTOM' || item.type == 'custom') && item.customEvent == 'chatroom_gift'">
                                    <text class="name">{{ item.nickName }}:</text>
                                    <text style="color: red">送给主播 {{ item.params.num }} 个 {{ giftModaData.giftData[item.params.id].name }}</text>
                                </view>

                                <view v-if="(item.contentsType == 'CUSTOM' || item.type == 'custom') && item.customEvent == 'chatroom_praise'">
                                    <text class="name">{{ item.nickName }}:</text>
                                    <text style="color: #497fe6">给主播点 {{ item.params.num }} 个赞</text>
                                </view>
                            </view>
                        </scroll-view>
                        <!-- 发送弹幕 -->
                        <view class="send">
                            <image v-if="!showInput" src="/static/images/ic_comments@2x.png" class="icon" @tap="showInputFun"></image>
                            <view style="position: fixed; right: 0">
                                <image
                                    src="/static/images/ic_Chat@2x.png"
                                    class="icon foot-icon"
                                    @tap="exitLiveRoom"
                                    style="background-color: rgba(105, 16, 16, 0.8); border-radius: 50%"
                                ></image>
                                <image v-if="giftModaData.identity !== 'compere'" src="/static/images/ic_Share@2x.png" class="icon foot-icon like-sty" @tap="giveLike"></image>
                                <image src="/static/images/ic_Gift@2x.png" class="icon foot-icon" @tap="giftModa"></image>
                            </view>
                        </view>
                        <!-- 输入框及发送按钮 -->
                        <view class="input-section" v-if="showInput">
                            <view class="danmu_btn">
                                <image style="width: 24px; height: 24px" :src="isDanmu ? '/images/danmuOpen.png' : '/images/danmuClose.png'" @tap="switchChange"></image>
                            </view>
                            <input
                                class="input_input"
                                :value="inputMessage"
                                focus="auto"
                                placeholder="请输入内容"
                                @input="bindInputMsg"
                                confirm-type="send"
                                @confirm="sendTextMsg"
                            />
                            <view style="height: 110rpx; width: 170rpx; display: flex; align-items: center; justify-content: center">
                                <view class="send_btn" @tap="sendTextMsg">
                                    <image class="send_btn_text" src="/static/images/send.png" />
                                </view>
                            </view>
                        </view>
                    </view>
                </view>
            </view>
        </view>
        <!-- 主播详情弹窗 -->
        <mp-dialog :show="hostShow" @buttontap="tapDialogButton" :buttons="buttons">
            <view class="userInfo">
                <view class="left">
                    <image src="/static/images/head.png" class="avatar"></image>
                    <view class="info">
                        <view class="username">{{ owner }}</view>
                        <view class="gift">
                            <text>
                                礼物
                                <text class="num">0</text>
                            </text>
                            <text class="fans">
                                粉丝
                                <text class="num">0</text>
                            </text>
                            <text class="fans">
                                被赞
                                <text class="num">0</text>
                            </text>
                        </view>
                    </view>
                </view>
                <view v-if="giftModaData.identity !== 'compere'">
                    <view class="right" v-if="isAttention">
                        <view class="attention" @tap="clickAttention">
                            <text class="fa fa-plus"></text>
                            <text>关注</text>
                        </view>
                    </view>
                    <view class="right" v-else>
                        <view class="attentionCancel" @tap="clickAttention">
                            <mp-icon type="field" icon="close" color="#fff" :size="18"></mp-icon>
                            <text>取消关注</text>
                        </view>
                    </view>
                </view>
            </view>
        </mp-dialog>
        <!-- 礼物模块 -->
        <!-- parse <template is="giftModa" :data="...giftModaData" v-if="showGiftModa"></template> -->
        <block name="giftModa">
            <view class="giftModa">
                <view class="mask" @tap="closeMask"></view>
                <!-- 观众送礼模块 -->
                <view v-if="identity !== 'compere'" style="width: 100%; padding: 20rpx; font-size: 15px; color: rgba(255, 255, 255, 0.8)">
                    <image src="/static/images/gift@2x.png" style="width: 13px; height: 13px; margin-right: 10px" />
                    <text class="giftModa-item">礼物</text>
                    <view style="margin: 5% 0">
                        <view
                            :class="'gift-item ' + (showGiftId === giftData.key && 'active')"
                            :data-giftid="giftData"
                            @tap="inselected"
                            v-for="(giftData, index) in giftData"
                            :key="giftData.value"
                        >
                            <image class="giftImg" :src="giftData.url" />

                            <text>{{ giftData.name }}</text>
                        </view>
                    </view>
                    <view style="float: right" v-if="showGivesModa">
                        <text class="gift-text">{{ giftName }}</text>
                        <form @submit="giftSubmit">
                            <view class="section">
                                <input
                                    class="input_input"
                                    name="giftNum"
                                    :value="giftNumValue"
                                    maxlength="3"
                                    placeholder="1"
                                    confirm-type="send"
                                    type="number"
                                    style="max-width: 50px; padding-left: 0; text-align: center; color: #000000"
                                />
                            </view>
                            <view class="btn-area">
                                <button
                                    formType="submit"
                                    plain
                                    style="
                                        width: 180rpx;
                                        background: linear-gradient(325deg, rgba(90, 93, 208, 1) 0%, rgba(4, 174, 240, 1) 100%);
                                        border-radius: 15px;
                                        color: #fff;
                                        font-size: 13px;
                                    "
                                >
                                    赠送
                                </button>
                            </view>
                        </form>
                    </view>
                </view>
                <!-- 主播查看礼物模块 -->
                <view v-else style="width: 100%; padding: 20rpx; font-size: 15px; color: rgba(255, 255, 255, 0.8)">
                    <image src="/static/images/gift@2x.png" style="width: 13px; height: 13px; margin-right: 10px" />
                    <text class="giftModa-item">礼物记录</text>
                    <view class="gift-record">
                        <text class="gf-text">共{{ 0 }}份</text>
                        <text class="gf-text">打赏人数{{ 0 }}人</text>
                    </view>
                </view>
            </view>
        </block>
        <!-- 成员列表模块 -->
        <!-- parse <template is="memberListModa" :data="...memberListModa" v-if="showMemberListModa"></template> -->
        <block name="memberListModa">
            <view class="memberListModa">
                <view class="mask" @tap="closeMask"></view>
                <!-- 观众视角模块 -->
                <view class="member-list" v-if="type !== 'compere'">
                    <image src="/static/images/user2.png" style="width: 15px; height: 15px; margin-right: 10px" />
                    <text class="memberListModa-item">观众{{ affiliations_count }}</text>
                    <!-- 人员列表 -->
                    <view class="child-view">
                        <view :data-giftid="memberList" style="height: 80rpx; display: flex; align-items: center" v-for="(memberList, index) in list" :key="index">
                            <image src="/static/images/head.png" style="width: 28px; height: 28px; border-radius: 50%; margin-right: 10px" />

                            <text>{{ memberList.member || memberList.owner }}</text>

                            <text style="color: rgb(0, 195, 255); margin-left: 10px">{{ memberList.owner && '管理员' }}</text>
                        </view>
                    </view>
                </view>
                <!-- 主播视角 -->
                <view class="weui-tab-member" v-else>
                    <view class="weui-navbar-member">
                        <block v-for="(item, index) in tabs" :key="index">
                            <view :id="index" :class="'weui-navbar__item-member ' + (activeIndex == index ? 'weui-bar__item_on-member' : '')" @tap="tabClick">
                                <view class="weui-navbar__title-member">{{ item }}</view>
                            </view>
                        </block>
                        <view
                            class="weui-navbar__slider-member"
                            :style="'left: ' + sliderLeft + 'px; transform: translateX(' + sliderOffset + 'px); -webkit-transform: translateX(' + sliderOffset + 'px);'"
                        ></view>
                    </view>
                    <!-- 观众 -->
                    <view class="weui-tab__panel-member">
                        <view class="weui-tab__content-member" v-if="activeIndex == 0" style="top: 100rpx; padding: 10px">
                            <view style="float: right; padding-right: 10px">
                                <text>房间禁言</text>
                                <switch @change="changeSpeech" :checked="switchChecked" />
                            </view>
                            <view :data-giftid="memberList" style="height: 80rpx; display: flex; align-items: center" v-for="(memberList, index) in list" :key="index">
                                <image src="/static/images/head.png" style="width: 28px; height: 28px; border-radius: 50%; margin-right: 10px" />

                                <text>{{ memberList.member || memberList.owner }}</text>

                                <text style="color: rgb(0, 195, 255); margin-left: 10px">{{ memberList.owner && '管理员' }}</text>
                            </view>
                        </view>
                    </view>
                    <!-- 白名单 -->
                    <view class="weui-tab__panel">
                        <view class="weui-tab__content-member" v-if="activeIndex == 1" style="top: 100rpx; padding: 10px">
                            <view :data-giftid="memberList" style="height: 80rpx; display: flex; align-items: center" v-for="(memberList, index) in whiteList" :key="index">
                                <image src="/static/images/head.png" style="width: 28px; height: 28px; border-radius: 50%; margin-right: 10px" />

                                <text>{{ memberList.member || memberList.owner }}</text>

                                <text style="color: rgb(0, 195, 255); margin-left: 10px">
                                    {{ memberList.owner && '管理员' }}
                                </text>
                            </view>
                        </view>
                    </view>
                    <!-- 用户禁言 -->
                    <view class="weui-tab__panel" style="top: 100rpx; padding: 10px">
                        <view class="weui-tab__content-member" v-if="activeIndex == 2" style="top: 100rpx; padding: 10px">
                            <text>第一期暂未实现（我们将在第二期实现此功能）</text>
                        </view>
                    </view>
                </view>
            </view>
        </block>
    </view>
</template>

<script>
var sliderWidth = 96; // 需要设置slider的宽度，用于计算中间位置

import USERS from '../../utils/users';
import disp from '../../utils/dispatcher';
export default {
    components: {},
    data() {
        return {
            buttons: [
                {
                    text: '关闭'
                }
            ],

            height: 0,
            hostShow: false,

            //主播详情弹窗
            showGiftModa: false,

            // 礼物弹窗
            opacity: 0,

            showGiftHint: false,

            // 展示礼物特效
            isAttention: false,

            // 关注主播
            showMemberListModa: false,

            // 成员列表弹窗
            msgList: [],

            giftNum: '1',
            toView: '',

            //消息框自动滚动
            src: '/static/images/bgImg.jpg',

            showInput: false,
            isDanmu: false,
            roomId: '',
            nickName: '',
            myUserName: '',
            textMsg: '',
            audience: 0,

            giftModaData: {
                // 礼物模块数据（主播、粉丝）
                identity: '',

                //直播间身份（粉丝）
                showGivesModa: false,

                //显隐送礼模块
                giftName: '',

                giftNumValue: '1',

                // 礼物数量
                giftUrl: '',

                giftData: {
                    gift_1: {
                        key: '1',
                        value: 'gift_1',
                        name: '香水玫瑰',
                        url: '/static/images/Gift_01@2x.png'
                    },
                    gift_2: {
                        key: '2',
                        value: '2',
                        name: '心想事成',
                        url: '/static/images/Gift_02@2x.png'
                    },
                    gift_3: {
                        key: '3',
                        value: 'gift_3',
                        name: '比翼双飞',
                        url: '/static/images/Gift_03@2x.png'
                    },
                    gift_4: {
                        key: '4',
                        value: 'gift_4',
                        name: '生日蛋糕',
                        url: '/static/images/Gift_04@2x.png'
                    },
                    gift_5: {
                        key: '5',
                        value: 'gift_5',
                        name: '大礼包',
                        url: '/static/images/Gift_05@2x.png'
                    },
                    gift_6: {
                        key: '6',
                        value: 'gift_6',
                        name: '春花浪漫',
                        url: '/static/images/Gift_06@2x.png'
                    },
                    gift_7: {
                        key: '7',
                        value: 'gift_7',
                        name: '小狗狗',
                        url: '/static/images/Gift_07@2x.png'
                    },
                    gift_8: {
                        key: '8',
                        value: 'gift_8',
                        name: '金镯子',
                        url: '/static/images/Gift_08@2x.png'
                    }
                },

                showGiftId: 0,
                url: ''
            },

            memberListModa: {
                affiliations_count: '',
                // 观看成员人数
                list: [],
                // 成员列表模块数据
                whiteList: [],
                // 白名单列表
                switchChecked: false,
                //房间禁言
                tabs: ['观众', '白名单', '用户禁言'],
                type: '',
                activeIndex: 0,
                sliderOffset: 0,
                sliderLeft: 0
            },

            danmuList: [
                {
                    text: '这是我的弹幕',
                    color: '#ff0000',
                    time: 1
                },
                {
                    text: '小程序直播哦',
                    color: '#ff00ff',
                    time: 3
                }
            ],

            roomName: '',
            owner: '',
            sliderLeft: '',
            sliderOffset: '',
            commentHeight: '',
            widthFix: 0,
            name: '',
            inputMessage: '',
            identity: '',
            showGiftId: '',

            giftData: {
                key: '',
                value: '',
                url: '',
                name: ''
            },

            showGivesModa: '',
            giftName: '',
            giftNumValue: 0,
            type: '',
            affiliations_count: '',
            memberList: [],
            list: [],
            tabs: [],
            activeIndex: '',
            switchChecked: '',
            whiteList: []
        };
    },
    onUnload: function () {
        this.exitLiveRoom();
    },
    onLoad: function (option) {
        console.log('coption.query', option);
        var that = this;
        let userName = uni.getStorageSync('userName');
        let userInfo = JSON.parse(userName);
        let identity = 'giftModaData.identity';
        let type = 'memberListModa.type';
        that[memberListModa.type] = option.identity;
        that[giftModaData.identity] = option.identity;
        that.setData({
            roomId: option.id,
            roomName: option.name,
            owner: option.owner,
            myUserName: userInfo.userName,
            nickName: userInfo.nickName,
            audience: option.audience
        });
        uni.getSystemInfo({
            success: function (res) {
                that.setData({
                    sliderLeft: (res.windowWidth / that.memberListModa.tabs.length - sliderWidth) / 2,
                    sliderOffset: (res.windowWidth / that.memberListModa.tabs.length) * that.memberListModa.activeIndex,
                    commentHeight: res.windowHeight - 500,
                    height: res.windowHeight
                });
            }
        }); //收到普通消息

        disp.on('app.onTextMessage', function (message) {
            let nickName = USERS[parseInt(Math.random() * USERS.length)].nick;
            message.nickName = nickName;
            let msgList = that.msgList;
            msgList.push(message);
            that.setData({
                msgList: msgList,
                toView: `item${message.id}`
            });
        }); //收到自定义消息 包括弹幕 礼物 点赞

        disp.on('app.onCustomMessage', function (message) {
            let nickName = USERS[parseInt(Math.random() * USERS.length)].nick;
            message.nickName = nickName;
            let msgList = that.msgList;
            msgList.push(message);
            that.setData({
                msgList: msgList,
                toView: `item${message.id}`
            });
        });
    },
    methods: {
        tabClick: function (e) {
            let that = this;
            let sliderOffset = 'memberListModa.sliderOffset';
            let activeIndex = 'memberListModa.activeIndex';
            that[memberListModa.activeIndex] = e.currentTarget.id;
            that[memberListModa.sliderOffset] = e.currentTarget.offsetLeft;
            let val = e.currentTarget.id;

            switch (val) {
                case '1':
                    that.getChatRoomWhitelist();
                    break;

                case '2':
                    break;

                default:
                    break;
            }
        },

        //点击出现输入框
        showInputFun: function () {
            this.setData({
                showInput: true
            });
        },

        //隐藏输入框
        onHideInput: function () {
            this.setData({
                showInput: false
            });
        },

        // 显示主播详情页面
        showHostDetail() {
            this.setData({
                hostShow: true
            });
        },

        // 点击关注
        clickAttention() {
            this.setData({
                isAttention: !this.isAttention
            });
        },

        // 弹幕开关
        switchChange() {
            this.setData({
                isDanmu: !this.isDanmu
            });
        },

        tapDialogButton(e) {
            this.setData({
                hostShow: false
            });
        },

        // 键盘输入时触发
        bindInputMsg(e) {
            console.log('txt:', e.detail.value);
            this.setData({
                textMsg: e.detail.value
            });
        },

        //发送普通消息
        sendTextMsg() {
            let self = this;
            this.onHideInput();
            let tsxtMsg = this.textMsg;
            let roomId = this.roomId;
            let from = this.myUserName;
            let id = uni.WebIM.conn.getUniqueId(); // 生成本地消息id

            let msg = new uni.WebIM.message('txt', id); // 创建文本消息

            msg.set({
                msg: tsxtMsg,
                // 消息内容
                to: roomId,
                from,
                roomType: true,
                ext: {
                    nickName: this.nickName
                },
                //扩展消息
                success: function (id, serverMsgId) {
                    console.log('send private text Success');
                },
                fail: function (e) {
                    console.log('Send private text error');
                }
            });
            msg.setGroup('groupchat');
            console.log('msg', msg);
            uni.WebIM.conn.send(msg.body);
            msg.body.nickName = self.nickName;
            let msgList = self.msgList;
            msgList.push(msg.body);
            let lastMsg = msgList.slice(-1);
            let toView = lastMsg[0];
            this.setData({
                msgList: msgList,
                toView: `item${toView.id}`
            });
            console.log('msgList', msgList);
        },

        //发礼物消息
        sendGiftMsg(giftNum) {
            let self = this;
            let roomId = this.roomId;
            let from = this.myUserName;
            let id = uni.WebIM.conn.getUniqueId();
            let msg = new uni.WebIM.message('custom', id); //不同的礼物修改 id {gift_1: '香水玫瑰', gift_2: '心想事成', ...}

            msg.set({
                to: roomId,
                roomType: true,
                customEvent: 'chatroom_gift',
                customExts: {
                    note: self.giftModaData.giftName
                },
                params: {
                    id: 'gift_' + self.giftModaData.showGiftId,
                    num: giftNum
                },
                success: function () {
                    console.log('send private text Success');
                },
                fail: function () {},
                ext: {
                    nickName: self.nickName
                }
            });
            msg.setGroup('groupchat');
            console.log('msg', msg);
            uni.WebIM.conn.send(msg.body);
            msg.body.nickName = self.nickName;
            let msgList = self.msgList;
            msgList.push(msg.body);
            let lastMsg = msgList.slice(-1);
            let toView = lastMsg[0];
            this.setData({
                msgList: msgList,
                toView: `item${toView.id}`
            });
        },

        // 发点赞消息
        giveLike() {
            let self = this;
            let roomId = this.roomId;
            let from = this.myUserName;
            let id = uni.WebIM.conn.getUniqueId();
            let msg = new uni.WebIM.message('custom', id);
            msg.set({
                to: roomId,
                roomType: true,
                customEvent: 'chatroom_praise',
                customExts: {
                    note: '点赞'
                },
                params: {
                    num: 1
                },
                success: function () {
                    console.log('send private text Success');
                },
                fail: function () {},
                ext: {
                    nickName: self.nickName
                }
            });
            msg.setGroup('groupchat');
            console.log('msg', msg);
            uni.WebIM.conn.send(msg.body);
            msg.body.nickName = self.nickName;
            let msgList = self.msgList;
            msgList.push(msg.body);
            let lastMsg = msgList.slice(-1);
            let toView = lastMsg[0];
            this.setData({
                msgList: msgList,
                toView: `item${toView.id}`
            });
        },

        // 发弹幕消息
        sendSubtitles() {
            let self = this;
            let roomId = this.roomId;
            let from = this.myUserName;
            let id = uni.WebIM.conn.getUniqueId();
            let msg = new uni.WebIM.message('custom', id);
            msg.set({
                to: roomId,
                roomType: true,
                customEvent: 'chatroom_barrage',
                customExts: {
                    note: '弹幕'
                },
                params: {
                    txt: '弹幕'
                },
                success: function () {
                    console.log('send private text Success');
                },
                fail: function () {},
                ext: {
                    nickName: self.nickName
                }
            });
            msg.setGroup('groupchat');
            console.log('msg', msg);
            uni.WebIM.conn.send(msg.body);
            msg.body.nickName = self.nickName;
            let msgList = self.msgList;
            msgList.push(msg.body);
            this.setData({
                msgList: msgList
            });
        },

        // 退出直播间
        exitLiveRoom() {
            let obj = {
                roomId: this.roomId,
                identity: this.giftModaData.identity || '',
                myUserName: this.myUserName
            }; // 主播下播

            if (obj.identity === 'compere') {
                this.stopLive(obj);
            }

            uni.WebIM.conn.quitChatRoom({
                roomId: obj.roomId,
                success: successFun,
                error: errorFun
            });

            function successFun(res) {
                uni.switchTab({
                    url: '/pages/index/index'
                });
            }

            function errorFun(e) {
                console.log('退出失败', e);
            }
        },

        stopLive(obj) {
            uni.request({
                url: `https://a1.easemob.com/appserver/liverooms/${obj.roomId}/users/${obj.myUserName}/offline`,
                method: 'POST',
                header: {
                    'content-type': 'application/json',
                    Authorization: 'Bearer ' + getApp().globalData.token
                },

                success(res) {
                    console.log('res>>>', res);
                },

                fail(e) {
                    console.log('err>>>', e);
                }
            });
        },

        // 显示礼物弹窗
        giftModa() {
            this.setData({
                showGiftModa: true
            });
        },

        //显示成员列表弹窗
        changeMemberListModa() {
            let self = this;
            self.getLiveRoomsMember(callback);
            self.setData({
                showMemberListModa: true
            });

            function callback(res) {
                let affiliations_count = 'memberListModa.affiliations_count';
                let list = 'memberListModa.list';
                self[memberListModa.list] = res.data.affiliations;
                self[memberListModa.affiliations_count] = res.data.affiliations_count;
            }
        },

        // 关闭礼物、成员列表弹窗
        closeMask() {
            this.setData({
                showGiftModa: false,
                showMemberListModa: false
            });
        },

        // 当前选中的礼物
        inselected(e) {
            let id = e.currentTarget.dataset.giftid.key;
            let showGiftId = 'giftModaData.showGiftId';
            let showGivesModa = 'giftModaData.showGivesModa';
            let giftName = 'giftModaData.giftName';
            let giftUrl = 'giftModaData.url';
            this[giftModaData.url] = e.currentTarget.dataset.giftid.url;
            this[giftModaData.giftName] = e.currentTarget.dataset.giftid.name;
            this[giftModaData.showGivesModa] = true;
            this[giftModaData.showGiftId] = id;
        },

        giftSubmit(e) {
            let giftNum = e.detail.value.giftNum || '1';
            this.sendGiftMsg(giftNum);
            this.setData({
                showGiftModa: false,
                giftNum: giftNum,
                showGiftHint: true
            }); // 这样的实现简直拉垮，时间原因将就凑合吧

            setTimeout(() => {
                this.setData({
                    opacity: 1
                });
            }, 100);
            setTimeout(() => {
                this.setData({
                    opacity: 0,
                    showGiftHint: false
                });
            }, 2000);
        },

        //获取直播间成员详情
        getLiveRoomsMember(callback) {
            let liveroomid = this.roomId;
            uni.request({
                url: `https://a1.easemob.com/appserver/liverooms/${liveroomid}`,
                header: {
                    'content-type': 'application/json',
                    Authorization: 'Bearer ' + getApp().globalData.token
                },

                success(res) {
                    callback(res);
                },

                fail(e) {
                    callback(e);
                }
            });
        },

        // 获取白名单列表
        getChatRoomWhitelist() {
            let self = this;
            let liveroomid = this.roomId;
            uni.WebIM.conn.getChatRoomWhitelist({
                chatRoomId: liveroomid,
                success: successFun,
                error: errorFun
            });

            function successFun(res) {
                let whiteList = 'memberListModa.whiteList';
                self[memberListModa.whiteList] = res.entities;
            }

            function errorFun(e) {
                console.log('白名单列表错误>>', e);
            }
        },

        // 房间禁言
        changeSpeech(e) {
            if (e.detail.value) {
                this.standSpeech();
            } else {
                this.relieve();
            }
        },

        // 房间禁言
        standSpeech() {
            let self = this;
            let liveroomid = this.roomId;
            uni.WebIM.conn.disableSendChatRoomMsg({
                chatRoomId: liveroomid,
                success: successFun,
                error: errorFun
            });

            function successFun(res) {
                let switchChecked = 'memberListModa.switchChecked';
                self[memberListModa.switchChecked] = true;
            }

            function errorFun(e) {
                console.log('禁言失败>>', e);
                let switchChecked = 'memberListModa.switchChecked';
                self[memberListModa.switchChecked] = false;
            }
        },

        // 房间解除禁言
        relieve() {
            let self = this;
            let liveroomid = this.roomId;
            uni.WebIM.conn.enableSendChatRoomMsg({
                chatRoomId: liveroomid,
                success: successFun,
                error: errorFun
            });

            function successFun(res) {
                let switchChecked = 'memberListModa.switchChecked';
                self[memberListModa.switchChecked] = false;
            }

            function errorFun(e) {
                console.log('解除禁言失败>>', e);
            }
        }
    }
};
</script>
<style>
/* @import '../../template/gift/gift.css';
@import '../../template/memberListModa/memberListModa.css'; */
 /* @import "../../style/weui.css"; */
.live-box {
    width: 100%;
    height: 240px;
}

.bg-img {
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
}

.live-box .player {
    width: 100%;
    height: 240px;
}

.weui-tab__content {
    text-align: center;
    background-color: rgba(0, 0, 0, 0.2);
    position: absolute;
    bottom: 100rpx;
    width: 100%;
}

.promt {
    margin: 20rpx;
    height: 100rpx;
    font-size: 28rpx;
    text-align: justify;
    max-width: 52%;

    transition: 0.5s;
    -moz-transition: 0.5s; /* Firefox 4 */
    -webkit-transition: 0.5s; /* Safari and Chrome */
    -o-transition: 0.5s; /* Opera */
}

.lw-style {
    height: 70rpx;
    background: rgba(0, 0, 0, 0.6);
    border-radius: 39px;
    color: #ffffff;
    display: flex;
    justify-content: left;
    align-items: center;
}

.lwImg {
    width: 50px !important;
    height: 50px !important;
}

.head-style {
    width: 28px;
    height: 28px;
    border-radius: 50%;
    margin-left: 7px;
}

.comment-list {
    width: 100%;
    box-sizing: border-box;
    padding: 0 20rpx;
    margin-bottom: 20rpx;
    font-size: 28rpx;
    text-align: left;
}
.like-sty:active {
    position: relative;
    top: 3px;
    background-color: rgba(0, 184, 240, 0.7);
    border-radius: 50%;
    transition: 0.2;
}

.name {
    color: #ffffff;
}

.send {
    position: fixed;
    bottom: 16rpx;
    left: 0;
    width: 100%;
    height: 100rpx;
    box-sizing: border-box;
    padding: 0 20rpx;
    display: flex;
    align-items: center;
    z-index: 777;
}

.send .write {
    width: 80%;
    height: 72rpx;
    line-height: 72rpx;
    border-radius: 20px;
    background: white;
    position: relative;
}

.write .input {
    width: 90%;
    height: 72rpx;
    font-size: 28rpx;
    color: #333;
    padding-left: 8rpx;
    margin-left: 10rpx;
}

.ok {
    padding-left: 30rpx;
    border-left: 1px solid #e6e6e6;
    position: absolute;
    top: -6rpx;
    right: 30rpx;
    text-align: center;
    color: #c1c1c1;
}

.send .icon {
    width: 32px;
    height: 32px;
}

.navbar {
    display: flex;
    position: absolute;
    z-index: 500;
    top: 15rpx;
    width: 100%;
    padding: 0 25rpx;
}

.user-style {
    height: 70rpx;
    background: rgba(0, 0, 0, 0.4);
    border-radius: 39px;
    color: #ffffff;
    display: flex;
    justify-content: left;
    align-items: center;
    font-size: 12px;
}

.user-right-style {
    position: fixed;
    right: 15px;
    top: 15px;
    display: flex;
    align-items: center;
}

.numPeople {
    color: #ffffff;
    width: 100rpx;
    height: 70rpx;
    background: rgba(0, 0, 0, 0.6);
    border-radius: 18px;
    font-size: 12px;
    margin-left: 10px;
    text-align: center;
    line-height: 3;
}

.foot-icon {
    margin-right: 10px;
}

.icon-bg {
    width: 42px;
    height: 42px;
    border-radius: 50%;
    background-color: rgba(0, 0, 0, 0.2);
}

.elipsis {
    width: 24px;
    height: 24px;
}

/* 主播 */

.userInfo {
    /* width           : 100%;
  box-sizing      : border-box;
  padding         : 30rpx 20rpx;
  display         : flex;
  justify-content : space-between;
  align-items     : center;
  border-bottom   : 1px solid #e6e6e6;
  background-color: #fff; */
}

.userInfo .left {
    /* display        : flex;
  justify-content: flex-start;
  align-items    : center; */
}

.userInfo .left .avatar {
    /* width        : 120rpx;
  height       : 120rpx;
  border-radius: 50%;
  margin-right : 20rpx; */
    width: 200rpx;
    height: 200rpx;
    border-radius: 50%;
}

.userInfo .info {
    /* font-size : 28rpx;
  text-align: left; */
}

.userInfo .left .username {
    font-size: 36rpx;
    font-weight: 600;
    margin: 10px 0;
}

.userInfo .card {
    /* margin-bottom: 8rpx; */
}

.userInfo .gift {
    font-size: 26rpx;
    color: #c1c1c1;
}
.userInfo .gift .num {
    color: #000;
    display: inline-block;
}

.userInfo .gift .fans {
    margin-left: 20rpx;
}

.userInfo .attentionCancel {
    width: 100px;
    height: 70rpx;
    line-height: 70rpx;
    border-radius: 20px;
    font-size: 28rpx;
    color: #fff;
    background-color: rgb(167, 167, 167);
    margin: 10px auto 0;
}

.userInfo .attention {
    width: 100px;
    height: 70rpx;
    line-height: 70rpx;
    border-radius: 20px;
    font-size: 28rpx;
    color: #fff;
    background: linear-gradient(325deg, rgba(90, 93, 208, 1) 0%, rgba(4, 174, 240, 1) 100%);
    margin: 10px auto 0;
}

.userInfo .okAttention {
    width: 80px;
    height: 70rpx;
    line-height: 70rpx;
    border-radius: 20px;
    font-size: 28rpx;
    color: #555;
    background-color: #e6e6e6;
}

.notice {
    width: 100%;
    box-sizing: border-box;
    padding: 20rpx;
    text-align: justify;
    font-size: 28rpx;
    border-bottom: 1px solid #e6e6e6;
    background-color: #fff;
}

.operation {
    width: 100%;
    height: 40px;
    font-size: 28rpx;
    box-sizing: border-box;
    padding: 0 20rpx;
    margin: 10rpx 0;
    display: flex;
    justify-content: space-between;
    align-items: center;
    color: #000;
    background: #fff;
}

.pop {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 140rpx;
    display: flex;
    justify-content: space-around;
    align-items: center;
    border-top: 1px solid #e6e6e6;
    background: #ffffff;
}

.report {
    width: 32px;
    height: 32px;
    margin-top: 6rpx;
    margin-bottom: 4rpx;
}

.pop .flex {
    display: flex;
    flex-direction: column;
    justify-content: center;
    font-size: 28rpx;
    color: #c1c1c1;
    position: relative;
}

.share-btn {
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    opacity: 0;
}

.input-section {
    position: absolute;
    display: flex;
    align-items: center;
    height: 110rpx;
    bottom: 0rpx;
    left: 0rpx;
    right: 0rpx;
    z-index: 500;
    background: #fff;
}

.input_input {
    background: #efefef;
    z-index: 500;
    width: 580rpx;
    height: 90rpx;
    padding-left: 35rpx;
    border-radius: 8px;
}

.danmu_btn,
.send_btn {
    width: 110rpx;
    height: 66rpx;
    z-index: 999;
    border-radius: 10rpx;
    display: flex;
    align-items: center;
    justify-content: center;
}

.send_btn {
    right: 30rpx;
    position: absolute;
    top: 24rpx;
}

.danmu_btn {
    left: 30rpx;
}

.danmu_text,
.send_btn_text {
    display: flex;
    width: 24px;
    height: 24px;
    color: #fff;
    z-index: 560;
}
</style>
