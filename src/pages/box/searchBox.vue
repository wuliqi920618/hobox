<template>
  <div class="page" v-if="state==2" style="margin: 0;padding: 0">
    <div class="findBox">
      <p>发现新盒子</p>
      <div>
        <img src="../../../static/icon/hobox.jpg" alt="" style="width: 100%;height: 264rpx">
        <span>路由器名称：{{wifi.SSID}}</span>
      </div>
      <h5 @click="wxbind()">立即绑定</h5>
    </div>
    <div class="bindAlready">
      <p class="title">已绑定设备</p>
      <ul>
        <li v-for="(item,index) in oldDevList" :key="index">
          <h6>{{item.essid}}</h6>
          <p>{{item.rmac}}</p>
        </li>
      </ul>
    </div>

  </div>
  <div class="page" v-else-if="state==1"
       style="background: #fff;width: 100%;height: 100%;margin: 0;padding: 0;padding-top: 60rpx">

    <div class="noBox">
      <img src="../../../static/icon/fail.png" alt="">
      <p>未发现家小盒，请重新搜索添加</p>
      <h4 @click="searchBox()">重新搜索</h4>
    </div>
    <div class="descript">
      <h6>温馨提示</h6>
      <p>家小盒匹配失败的排查方法: </p>
      <p>1.请确认设备是否连接到带有家小盒的无线网络: </p>
      <p>2.请确认家小盒的接口连接是否正常: </p>
      <p>3.请确认路由器是否有网络:</p>
    </div>

  </div>
  <div v-else></div>
</template>

<script>
  export default {
    data () {
      return {
        // 0 页面初始化 1 未发现家小盒 2 发现家小盒
        state: 0,
        wifi: {},
        user: {},
        oldDevList: []
      }
    },
    onLoad () {
      this.state = 0
    },
    onShow () {
      this.user = wx.getStorageSync('user')
      this.searchBox()
      this.getOldDevList()
    },
    methods: {
      getOldDevList () {
        var _this = this
        this.$api.get('/box/all', null, null, r => {
          _this.oldDevList = r.data
          console.log(_this.oldDevList)
        })
      },
      searchBox () {
        wx.showLoading({
          title: '加载中'
        })
        let _this = this
        wx.startWifi({
          success: function (res) {
            wx.getConnectedWifi({
              complete: r => {
                wx.hideLoading()
              },
              success: res => {
                res.wifi['bssidStr'] = res.wifi.BSSID.toUpperCase()
                _this.wifi = res.wifi
                // _this.wxbind()
                _this.state = 2
              },
              fail: err => {
                _this.state = 1
              }
            })
          }
        })
      },
      wxbind () {
        console.log('11111')
        let bssid = this.wifi.BSSID
        bssid = bssid.replace(/:/g, '')
        this.$api.post('/box/wxbind', {'ssid': this.wifi.SSID, 'bssid': parseInt(bssid, 16)}, null, r => {
          console.log('22222')
          if (r.data) {
            this.user['box'] = r.data
            this.user['box'].bmacStr = this.$api.ToMac(this.user['box'].bmac)
            this.state = 2
            this.setbox()
          } else {
            this.state = 1
          }
        })
      },
      setbox () {
        console.log('33333')
        let box = this.user.box
        wx.request({
          url: 'https://www.hobox.com.cn:8082/api/test/app/usrbox',
          data: {'bmac': box.bmac, 'rmac': box.rmac},
          method: 'POST',
          header: {
            'Cookie': wx.getStorageSync('sessionId')
          },
          success: res => {
            console.log(res.data.statusCode)
            // 设置盒子后，更改session
            if (res.data.statusCode == 200) {
              wx.setStorageSync('user', this.user)
              wx.setStorageSync('sessionId', res.header['Set-Cookie'])
              wx.redirectTo({
                url: '/pages/box/bindSuccess'
              })
              wx.setStorageSync('wxbind', true)
            } else if (res.data.statusCode == 301) {
              console.log('301')
              wx.redirectTo({
                url: '/pages/box/bindFail'
              })
            } else {
              wx.showToast({
                title: res.data.message,
                icon: 'none',
                duration: 3000
              })
            }
          }
        })
      }
    }
  }
</script>

<style scoped>

  .findBox{
    width: 90%;
    margin: 0 auto;
    border: 1rpx solid transparent;
    min-height: 514rpx;
    border-radius: 15rpx;
    box-shadow: 0 0 10rpx #999;
    margin-bottom: 20rpx;
    margin-top: 20rpx;
    position: relative;
  }
  .findBox p{
    text-align: center;
    margin-bottom: 20rpx;
    color: #000000;
    font-size: 32rpx;
    height: 44rpx;
    margin-top: 20rpx;
    border: 1rpx solid transparent;
  }
  .findBox div{
    width: 92%;
    margin: 0 auto;
    height: 264rpx;
    position: relative;
    border: 1rpx solid transparent;
    margin-bottom: 20rpx;
  }

  .findBox div span{
    position: absolute;
    left: 0;
    bottom: 0;
    display: inline-block;
    width: 100%;
    height: 50rpx;
    color: #fff;
    text-align: left;
    line-height: 50rpx;
    background: rgba(0,0,0,0.3);
    font-size: 20rpx;
    text-indent: 20rpx;
  }
.findBox h5{
  margin: 0 auto;
  width: 310rpx;
  height: 80rpx;
  background: #01CDE8;
  line-height: 80rpx;
  text-align: center;
  color: #fff;
  border-radius: 60rpx;
  box-shadow: 1rpx 1rpx 2px #01CDE8;
   position: absolute;
  left: 50%;
  margin-left: -155rpx;
  bottom: 40rpx;
}
.bindAlready{
  width: 90%;
margin: 0 auto;
  margin-top: 80rpx;
}
.bindAlready p.title{
  color: #999999;
  font-size: 24rpx;
  border-bottom:1rpx solid #ddd;
   text-indent: 30rpx;
  padding-bottom: 10rpx;
}
  .bindAlready ul li{
    height: 140rpx;
    border-bottom:1rpx solid #eeeeee;
    text-align: left;
  }
  .bindAlready ul li h6{
color: #222222;
    height: 80rpx;
line-height: 65rpx;
    font-size: 32rpx;
    margin-left: 30rpx;
   font-weight: 500;
    font-family:PingFangSC-Regular;
  }
  .bindAlready ul li p{
    height: 60rpx;
    line-height: 41rpx;
    color: #999999;
    font-size: 24rpx;
    margin-left: 30rpx;
    font-family:PingFangSC-Regular;
  }
  .noBox{
    text-align: center;
  }
  .noBox img{
width: 200rpx;
    height:186rpx ;
    border: 1rpx solid transparent;
  }
  .noBox p{
color: #999999;
    font-size: 14px;
    margin-top: 40rpx;
    margin-bottom:100rpx;
  }
  .noBox h4{
    background: #01CDE8;
    height: 84rpx;
    line-height: 84rpx;
    width: 304rpx;
    color: #fff;
    font-size: 30rpx;
    box-shadow: 1rpx 1rpx 2px #01CDE8;
    border-radius: 40rpx;
    margin: 0 auto;
  }
  .descript{
    color: #01CDE8;
    width: 100%;
    height: 300rpx;
    border-top: 1rpx dashed #b2b2b2;
    position: absolute;
    left: 0;
    bottom: 0;
    padding-top: 30rpx;
  }
  .descript h6{
    font-size: 28rpx;
    text-indent: 60rpx;
    margin-top: 20rpx;
  }
  .descript p{
    font-size: 24rpx;
    text-indent: 60rpx;
  }
</style>
