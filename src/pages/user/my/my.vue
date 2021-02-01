<template>
  <view class="">
    <view class="center">
      <view class="logo" @click="bindLogin" :hover-class="!hasLogin ? 'logo-hover' : ''">
        <image class="my-bg" :src="bannerBg"></image>
        <view class="my-container">
          <image class='logo-img' :src="avatarUrl"></image>
          <view class="logo-title">
            <text class="uer-name">{{ hasLogin ? userName : '未登录' }}</text>
            <text class="iconfont white icon-arrow-right" v-if="!hasLogin"></text>
          </view>
        </view>
      </view>
      <view class="center-list" v-for="(item,index) in moduleDataFilter" :key="index">
        <view class="center-list-item" v-for="(ite,idx) in item" :key="idx">
          <text :class="['iconfont',ite.icon]"></text>
          <text class="list-text">{{ite.name}}</text>
          <text class="iconfont default icon-arrow-right"></text>
        </view>
      </view>
      <view class="center-list" v-for="(item,index) in moduleDataFilter" :key="index">
        <view class="center-list-item" v-for="(ite,idx) in item" :key="idx">
          <text :class="['iconfont',ite.icon]"></text>
          <text class="list-text">{{ite.name}}</text>
          <text class="iconfont default icon-arrow-right"></text>
        </view>
      </view>
      <view class="center-list" v-for="(item,index) in moduleDataFilter" :key="index">
        <view class="center-list-item" v-for="(ite,idx) in item" :key="idx">
          <text :class="['iconfont',ite.icon]"></text>
          <text class="list-text">{{ite.name}}</text>
          <text class="iconfont default icon-arrow-right"></text>
        </view>
      </view>
      <view class="btn-row">
        <button v-if="hasLogin" class="primary" type="primary" :loading="logoutBtnLoading" @tap="bindLogout"> 退出登录
        </button>
      </view>
    </view>
  </view>
</template>
<script>
import {
  mapState,
  mapMutations
} from 'vuex'
import {
  univerifyLogin
} from "@/common/univerify.js"
import avatarUrl from '@/static/img/logo.png'
import bannerBg from '@/static/img/user-bg.png'
export default {
  data () {
    return {
      avatarUrl: avatarUrl,
      inviteUrl: avatarUrl,
      bannerBg: bannerBg,
      logoutBtnLoading: false,
      hasPwd: uni.getStorageSync('uni_id_has_pwd'),
      module: [
        {
          name: "扫一扫",
          icon: "&#xe6c6;",
          link: "/"
        }
      ],
      moduleData: [
        [ {
          name: "新消息通知",
          icon: "icon-comment",
          link: "/",
          isLogin: false
        } ],
        [ {
          name: "帮助与反馈",
          icon: "icon-prompt",
          link: "/",
          isLogin: false
        },
        {
          name: "服务条款及隐私",
          icon: "icon-explain",
          link: "/",
          isLogin: false
        } ],
        [
          {
            name: "关于引用",
            icon: "icon-help",
            link: "/",
            isLogin: false
          }
        ], [
          {
            name: "邀请好友",
            icon: "icon-share",
            link: "/",
            isLogin: true
          },
        ],
        [
          {
            name: "修改密码",
            icon: "icon-lock",
            link: "/",
            isLogin: true
          }
        ],
        [
          {
            name: "退出登录",
            icon: "icon-sign-out",
            link: "/",
            isLogin: true
          }
        ]
      ]
    }
  },
  computed: {
    ...mapState([ 'hasLogin', 'forcedLogin', 'userName' ]),
    moduleDataFilter () {
      if (this.hasLogin) {
        return this.moduleData
      }
      return this.moduleData.filter(item => item.filter(ite => !ite.isLogin).length > 0)
    }
  },
  created () {
    // uni.setNavigationBarTitle({
    //   title: '新的标题'
    // });
    // uni.setNavigationBarColor({
    //   frontColor: '#ffffff',
    //   backgroundColor: '#ff0000',
    //   animation: {
    //     duration: 400,
    //     timingFunc: 'easeIn'
    //   }
    // })
  },
  methods: {
    ...mapMutations([ 'logout' ]),
    bindLogin () {
      if (!this.hasLogin) {
        univerifyLogin().catch((err) => {
          if (err === false) return
          uni.navigateTo({
            url: "/pages/user/login/login"
          })
        })
      }
    },
    bindLogout () {
      const loginType = uni.getStorageSync('login_type')
      if (loginType === 'local') {
        this.logout()
        if (this.forcedLogin) {
          uni.reLaunch({
            url: '/pages/user/login/login'
          })
        }
        return
      }
      this.logoutBtnLoading = true
      uniCloud.callFunction({
        name: 'user-center',
        data: {
          action: 'logout'
        },
        success: (e) => {
          console.log('logout success', e)
          if (e.result.code == 0) {
            this.logout()
            uni.removeStorageSync('uni_id_token')
            uni.removeStorageSync('username')
            uni.removeStorageSync('uni_id_has_pwd')
            /**
             * 如果需要强制登录跳转回登录页面
             */
            this.inviteUrl = ''
            if (this.forcedLogin) {
              uni.reLaunch({
                url: '/pages/user/login/login'
              })
            }
          } else {
            uni.showModal({
              content: e.result.msg,
              showCancel: false
            })
            console.log('登出失败', e)
          }
        },
        fail: (e) => {
          uni.showModal({
            content: JSON.stringify(e),
            showCancel: false
          })
        },
        complete: () => {
          this.logoutBtnLoading = false
        }
      })
    },
    toInvite () {
      uni.navigateTo({
        url: '/pages/user/invite/invite'
      })
    },
    goto () {
      uni.navigateTo({
        url: '/pages/user/pwd/update-password'
      })
    }
  }
}
</script>
<style>
.modules-card{
  border-radius: 12rpx;
}
.modules-card::after{
  border:none;
  border-radius: 12rpx;
}
.uni-card__header-title-text::after{
  border-bottom:none !important;
}
  page,view{
    display:flex;
  }

  button {
    width: 100%;
  }
  .my-bg{
    width:100%;
    height:100%;
  }
  .my-container{
    width:100%;
    padding: 120rpx 20rpx 0;
    box-sizing: border-box;
    /* background-color: #24bd7a; */
    flex-direction: row;
    align-items: center;
    position: absolute;
  }
  .center {
    flex-direction: column;
  }
  .server{
    width:750rpx;
    padding:0 30rpx;
    box-sizing:border-box;
    margin-top:-70rpx
  }
  .logo {
    width: 750rpx;
    height: 320rpx;

    position: relative;
  }
  .logo-hover {
    opacity: 0.8;
  }

  .logo-img {
    width: 120rpx;
    height: 120rpx;
    border-radius: 150rpx;
  }

  .logo-title {
    height: 150rpx;
    flex: 1;
    align-items: center;
    justify-content: space-between;
    flex-direction: row;
    margin-left: 20rpx;
  }

  .uer-name {
    height: 60rpx;
    line-height: 60rpx;
    color: #ffffff;
  }

  .go-login {
    color: #ffffff;
  }

  .login-title {
    height: 150rpx;
    align-items: self-start;
    justify-content: center;
    flex-direction: column;
    margin-left: 20rpx;
  }

  .center-list {
    background-color: #ffffff;
    margin-top: 20rpx;
    width: 750rpx;
    flex-direction: column;
  }

  .center-list-item {
    height: 90rpx;
    width: 750rpx;
    box-sizing: border-box;
    flex-direction: row;
    padding: 0rpx 20rpx;
  }

  .border-bottom {
    border-bottom-width: 1rpx;
    border-color: #c8c7cc;
    border-bottom-style: solid;
  }


  .list-text {
    height: 90rpx;
    line-height: 90rpx;
    color: #555;
    flex: 1;
    text-align: left;
  }
</style>