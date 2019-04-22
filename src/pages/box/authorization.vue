<template>
  <div class="page" v-if="show">
    <div class="pointDiv">
      <open-data type="userAvatarUrl"></open-data>
      <open-data type="userNickName"></open-data>
    </div>
    <!-- 需要使用 button 来授权登录 -->
    <div>
      <button v-if="canIUse" open-type="getUserInfo" @getuserinfo="bindGetUserInfo()" class="weui-btn authButton">授权登录
      </button>
      <div v-else>请升级微信版本</div>
    </div>
  </div>
  <div v-else style="background: #ffffff;position: fixed;height: 100%;width: 100%;display: flex;">
    <img src="/static/icon/img_launch.jpg" style="width: 100%;height: 100%"/>
  </div>
</template>

<script>
  const baseUrl = 'https://www.hobox.com.cn:8082/api/test/app'
  import api from '@/api/index'

  export default {
    data () {
      return {
        canIUse: wx.canIUse('button.open-type.getUserInfo'),
        code: '',
        unionid: '',
        userInfo: {},
        user: {},
        loginType: 0,
        show: false
      }
    },
    onReady () {
      this.bindGetUserInfo()
    },
    methods: {
      bindGetUserInfo () {
        var _this = this
        wx.login({
          success: res => {
            var code = res.code
            // 查看是否授权
            wx.getSetting({
              success: function (res) {
                if (res.authSetting['scope.userInfo']) {
                  // 已经授权，可以直接调用 getUserInfo 获取头像昵称
                  wx.getUserInfo({
                    success: function (res) {
                      _this.userInfo = res.userInfo
                      wx.request({
                        url: baseUrl + '/getunionid',
                        data: {'code': code, 'iv': res.iv, 'encryptedData': res.encryptedData},
                        method: 'POST',
                        success: r => {
                          console.log(r)
                          if (r.data.unionid) {
                            _this.unionid = r.data.unionid
                            _this.unionidlogin()
                          }
                        },
                        fail: err => {
                          wx.showToast({
                            title: '人员信息获取失败：' + err,
                            icon: 'none',
                            duration: 3000
                          })
                        }
                      })
                    },
                    fail: function (res) {
                      wx.showToast({
                        title: '人员信息获取失败：' + res,
                        icon: 'none',
                        duration: 3000
                      })
                    }
                  })
                } else {
                  _this.show = true
                }
              },
              fail: function (res) {
                wx.showToast({
                  title: '授权获取失败：' + res,
                  icon: 'none',
                  duration: 3000
                })
              }
            })
          },
          fail: res => {
            wx.showToast({
              title: '微信登录失败：' + res,
              icon: 'none',
              duration: 3000
            })
          }
        })
      },
      wxlogin (params) {
        var _this = this
        wx.request({
          url: baseUrl + '/login/wechat',
          data: params,
          method: 'POST',
          success: res => {
            wx.setStorageSync('sessionId', res.header['Set-Cookie'])
            _this.user = res.data.data
            if (res.data.statusCode === 200) {
              // 如果当前用户存在绑定盒子，直接跳转到首页；不存在则进行绑定
              wx.setStorageSync('wxbind', true)
            } else {
              wx.setStorageSync('wxbind', false)
            }
            wx.setStorageSync('user', _this.user)
            setTimeout(_ => {
              wx.switchTab({
                url: '/pages/box/home'
              })
            }, 1000)
          },
          fail: err => {
            wx.showToast({
              title: '登录失败：' + err,
              icon: 'none',
              duration: 3000
            })
          }
        })
      },
      unionidlogin () {
        wx.request({
          url: baseUrl + '/usr/' + this.unionid,
          method: 'GET',
          success: r => {
            if (r.data.data && r.data.data['box']) {
              let user = r.data.data
              this.wxlogin({'wxuuid': this.unionid, 'bmac': user['box'].bmac, 'uname': this.userInfo.nickName})
            } else {
              this.wxlogin({'wxuuid': this.unionid, 'uname': this.userInfo.nickName})
            }
          }
        })
      }
    }
  }
</script>

<style scoped>
  .pointDiv {
    height: 50px;
    width: 50px;
    margin: 200rpx auto 200rpx auto;
    text-align: center;
    border-radius: 50px;
    position: relative;
  }
  .authButton{
    width: 580rpx;
    height: 84rpx;
    line-height: 84rpx;
    text-align: center;
    background: #00bcd4;
    color: #ffffff
  }
</style>
