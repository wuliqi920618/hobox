<template>
    <div class="page">
      <div class="weui-cells weui-cells_after-title">
        <div class="weui-cell" @click="userInfo">
          <div class="weui-cell__hd">
            <i-avatar v-bind:src="user.imageurl" size="large" i-class="personavatar"></i-avatar>
          </div>
          <div class="weui-cell__bd">
            <h4 class="weui-media-box__title"style="margin-bottom: 16rpx">{{user.nickname}}    <span>账号：{{user.uname}}</span></h4>
            <p class="weui-media-box__desc">{{ user.box.essid }}</p>

          </div>
          <div class="weui-cell__ft weui-cell__ft_in-access"></div>
        </div>
      </div>

      <div style="padding: 20rpx 10px;background: #ffffff;margin-bottom: 30rpx">
        <div class="weui-grids plan">
          <block>
            <div class="weui-grid" style="border-right: 1px solid #dcdbdb" @click="boxChange">
              <img class="weui-grid__icon my_detail" src="/static/icon/qhhz.png" />
              <div class="weui-grid__label">盒子管理</div>
            </div>
            <div class="weui-grid" @click="goSwjh">
              <img class="weui-grid__icon my_detail" src="/static/icon/swjh.png" />
              <div class="weui-grid__label">限网计划</div>
            </div>
          </block>
        </div>
      </div>

      <div class="weui-cells weui-cells_after-title">
        <div class="weui-cell" @click="goPerson">
          <div class="weui-cell__hd">
            <div class="my_detail">
              <img src="../../../static/icon/user.png" class="my_img"/>
            </div>
          </div>
          <div class="weui-cell__bd" >
            <h4 class="weui-media-box__title">人员列表</h4>
          </div>
          <div class="weui-cell__ft weui-cell__ft_in-access"></div>
        </div>
        <div class="weui-cell" @click="goDev">
          <div class="weui-cell__hd">
            <div class="my_detail">
              <img src="../../../static/icon/device.png" class="my_img" />
            </div>
          </div>
          <div class="weui-cell__bd" >
            <h4 class="weui-media-box__title">设备列表</h4>
          </div>
          <div class="weui-cell__ft weui-cell__ft_in-access"></div>
        </div>
        <div class="weui-cell" @click="goAccount">
          <div class="weui-cell__hd">
            <div class="my_detail">
              <img src="/static/icon/zhgl.png" class="my_img" />
            </div>
          </div>
          <div class="weui-cell__bd" >
            <h4 class="weui-media-box__title">账号管理</h4>
          </div>
          <div class="weui-cell__ft weui-cell__ft_in-access"></div>
        </div>
        <div class="weui-cell" @click="goQuestions">
          <div class="weui-cell__hd">
            <div class="my_detail">
            <img src="/static/icon/bzfk.png" class="my_img" />
            </div>
          </div>
          <div class="weui-cell__bd" >
            <h4 class="weui-media-box__title">常见问题</h4>
          </div>
          <div class="weui-cell__ft weui-cell__ft_in-access"></div>
        </div>
        <div class="weui-cell" @click="goProtocol">
          <div class="weui-cell__hd">
            <div class="my_detail">
            <img src="/static/icon/gywm.png" class="my_img" />
            </div>
          </div>
          <div class="weui-cell__bd" >
            <h4 class="weui-media-box__title">关于我们</h4>
          </div>
          <div class="weui-cell__ft weui-cell__ft_in-access"></div>
        </div>
        <div class="weui-cell">
          <div class="weui-cell__hd">
            <div class="my_detail">
            <img src="/static/icon/bbh.png" class="my_img" />
            </div>
          </div>
          <div class="weui-cell__bd">
            <h4 class="weui-media-box__title">版本号</h4>
          </div>
          <div class="weui-cell__ft">V1.3.2</div>
        </div>
      </div>


    </div>
</template>

<script>
  export default {
    data () {
      return {
        user: {
          box: {
            essid: ''
          }
        }
      }
    },
    onReady () {
      this.user.box.essid = ''
    },
    onShow () {
      let _user = wx.getStorageSync('user')
      _user['imageurl'] = this.$api.ImgName(_user.uicon)
      this.user = _user
      if (!_user.box) {
        this.user.box = {}
      }
    },
    methods: {
      userInfo () {
        wx.navigateTo({
          url: '/pages/box/userinfo'
        })
      },
      boxChange () {
        let box = this.user.box
        wx.navigateTo({
          url: '/pages/box/boxChange?bid=' + box.bid
        })
      },
      goSwjh () {
        wx.navigateTo({
          url: '/pages/box/swjh'
        })
      },
      goAccount () {
        wx.navigateTo({
          url: '/pages/box/account'
        })
      },
      goProtocol () {
        wx.navigateTo({
          url: '/pages/box/protocol'
        })
      },
      goQuestions () {
        wx.navigateTo({
          url: '/pages/box/questions'
        })
      },
      goPerson () {
        wx.navigateTo({
          url: '/pages/box/personlist'
        })
      },
      goDev () {
        wx.navigateTo({
          url: '/pages/box/devonline'
        })
      }
    }
  }
</script>

<style scoped>
  .page{
    padding-top: 30rpx
  }
  .weui-cells:before {
    top: 0;
    border-top: 0px;
  }
  .weui-cells{
    margin-bottom: 30rpx;
  }
  .weui-cells:after {
    bottom: 0px;
    border-bottom: 0px;
  }
  .weui-grids {
    border-top: 0px;
    border-left: 0px;
  }
  .weui-grid{
    border-bottom: 0px;
    border-right: 0px;
    width: 50%;
    padding: 0px 0px;
  }
  .my_detail{
    height: 60rpx;
    width: 60rpx;
  }
  .my_img{
    width: 100%;
    height: 100%;
  }
  .weui-media-box__title span{
    font-size: 20rpx;
    margin-left: 40rpx;
    color: #999;
  }
  .plan{
    height: 80rpx;
  }
  .plan .weui-grid{
    position: relative;
    height: 80rpx;
  }
  .plan .weui-grid img{
    width: 40rpx;
    height: 40rpx;
    position: absolute;
    left:30%;
    top: 50%;
    margin-top: -20rpx;
    border:1px solid reds
  }
  .plan .weui-grid__label{
     margin-left: 40rpx;
    position: absolute;
    left:35%;
    top:15%;
  }
</style>
