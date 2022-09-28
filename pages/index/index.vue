<template>
    <view class="page">
        <view class="section" v-for="item in liveroomsList" :key="item.id">
            <card :list='item.data' cards='card' > </card> 
        </view>
    </view>
</template>

<script>
const app = getApp();
import disp from '../../utils/dispatcher';
import card from '../../template/card/card.vue'
export default {
    components:{
      card
    },
    data() {
        return {
            width: 0,
            height: 0,
            liveroomsList: [
                    {
                        label: '热门直播',
                        labelId: '10001',
                        data: []
                    }
            ],
            cursor: '',

            cardData: {
                id: '',
                cover: '',
                owner: '',
                affiliations_count: ''
            },

            list: []
        };
    },
    onLoad() {
        let self = this;
        disp.on('app.loginSuccess', function () {
            self.getLiveRooms(6, '', callback);
        });
        uni.getSystemInfo({
            success: function (res) {
                let w = res.windowWidth;
                let h = res.windowHeight;
                app.globalData.width = w;
                app.globalData.height = h;
                self.setData({
                    width: w,
                    height: h
                });
            }
        });

        function callback(res) {
            let list = res.data.entities;
            self.cursor = res.data.cursor;
            self.liveroomsList= [
                    {
                        label: '热门直播',
                        labelId: '10001',
                        data: list 
                    }
                ]
            console.log(self.liveroomsList)
        } //创建直播间
        // wx.request({
        //   url: 'https://a1.easemob.com/appserver/liverooms',
        //   method: 'POST',
        //   data: {
        //     name: '沙箱环境聊天室',
        //     description: '快来嗨',
        //     maxusers: 5000,
        //     owner: 'zdtest',
        //     members: ['zdtest2'],
        //     cover: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1585649298994&di=4a23a41095ba858b3275b9a9312eea81&imgtype=0&src=http%3A%2F%2Fa4.att.hudong.com%2F21%2F09%2F01200000026352136359091694357.jpg',
        //     ext: {test: true}
        //   },
        //   header: {
        //     'content-type': 'application/json',
        //     Authorization: 'Bearer YWMtAXs7anQAEeqWBpHuq-M6OegrzF8zZk2Wp8GS3pF-orBygEswZ3AR6oeZcaPHhOyhAwMAAAFxNTHrXQBPGgArP-xWEpAbJQqBqj54vLgVFeBCB6sd_lmQGS5o5vHOmA'
        //   },
        //   success (res) {
        //     console.log('创建的直播间: ', res)
        //   }
        // })
        //设置直播状态
        // let liveroomId = "111402651025409";
        // let username = "zdtest"
        // wx.request({
        //   url: `https://a1.easemob.com/appserver/liverooms/${liveroomId}/users/${username}/ongoing`,
        //   method: 'POST',
        //   header: {
        //     'content-type': 'application/json',
        //     Authorization: 'Bearer YWMtQDafZHMhEeqyFiOa7jOZEE1-S6DcShHjkNXh_7qs2vUy04pwHuER6YGUI5WOSRNCAwMAAAFxL34SkQBPGgCgcHDOxwPUcTnUwWhld-t9BIWlLRZ3xZiJwKGCPYtqew'
        //   },
        //   success (res) {
        //     console.log('开始直播: ', res)
        //   }
        // })
    },
    onPullDownRefresh() {
             console.log('ooooo')
        let self = this;
        self.getLiveRooms(6, self.cursor, callback);
         
        function callback(res) {
   
            if (!res.data.entities) {
                uni.showToast({
                    title: '已无更多数据',
                    icon: 'none',
                    duration: 2000
                });
                uni.stopPullDownRefresh();
                return;
            }

            let list = res.data.entities;
            let currentList = list.concat(self.liveroomsList[0].data);
            self.cursor = res.data.cursor;
            self.liveroomsList= [
                    {
                        label: '热门直播',
                        labelId: '10001',
                        data: currentList
                    }
                ]
            console.log(self.liveroomsList)
            console.log('list..', list);
            uni.stopPullDownRefresh();
        }
    },
    methods: {
        getLiveRooms(limit, cursor, callback) {
            let self = this;
            uni.request({
                url: 'https://a1.easemob.com/appserver/liverooms/ongoing',
                data: {
                    limit: limit,
                    cursor: cursor
                },
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

        detail() {
            uni.navigateTo({
                url: `/pages/detail/detail`
            });
        },

    }
};
</script>
<style>
/* @import '../../template/card/card.css'; */ /* 绿色#15b111 */

/* 箭头#c1c1c1 */

/* 分割线#e5e5e5 */
.page {
    background-color: #f2f2f2;
}

.head-swiper {
    height: 400rpx;
}

.head-title {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 40px;
    background: rgba(0, 0, 0, 0.4);
    display: flex;
    justify-content: flex-start;
    align-items: center;
}

.head-live {
    width: 48px;
    height: 48px;
    margin: 0 40rpx 0 30rpx;
}

.head-title .title {
    font-size: 28rpx;
    color: #fff;
}

.section {
    padding-bottom: 20rpx;
    border-bottom: 1px solid #e5e5e5;
    font-size: 28rpx;
}

.section .label-name {
    box-sizing: border-box;
    margin: 30rpx auto;
    padding: 0 30rpx;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.section .more {
    width: 16px;
    height: 16px;
}
</style>
