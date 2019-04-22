<template>
  <div class="page" style="background: #fff;width: 100%;height: 100%;margin: 0;padding: 0;padding-top: 60rpx">
    <div class="boxSuccess">
      <img src="../../../static/icon/success1.png" alt="">
      <h3>恭喜您成功绑定家小盒</h3>
      <p>您已成为该HOBOX的管理员 </p>
      <p>管理员将拥有该HOBOX的一切权力，赶快去添加家庭</p>
      <p>里面其他成员试试吧</p>
      <h4 @click="know()">关联我的手机</h4>
    </div>
    <div class="descript">
      <img src="../../../static/icon/success2.png" alt="">
    </div>
    <div class="modalPage" v-show="modalPage">
      <div class="linkPhone">
        <img src="../../../static/icon/phone.png" alt="">
        <h4>关联手机</h4>
        <p>您当前使用的手机是{{model}}是否需要关联到该账号</p>
        <span class="link" @click="link">关联</span>
        <span class="cancel" @click="cancel">取消</span>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        modalPage: false,
        model: ''
      }
    },
    onLoad () {
    },
    onShow () {
      setTimeout(_ => {
        this.getSys()
      }, 1000)
    },
    methods: {
      link () {
        this.modalPage = false
        this.devdata = wx.getStorageSync('devdata')
        let dev = this.devdata
        console.log(dev)
        var param = {
          pid: dev.pid
        }
        this.$api.put('/dev/' + dev.dmac, param, null, r => {
          wx.showToast({
            title: '操作成功',
            icon: 'success',
            duration: 1500
          })
        })
      },
      cancel () {
        this.modalPage = false
      },
      getSys () {
        wx.getSystemInfo({
          success: function (res) {
            console.log(res)
          }
        })
      },
      know () {
        var wxbind = true
        wx.setStorageSync('wxbind', wxbind)
        setTimeout(_ => {
          wx.navigateTo({
            url: '/pages/box/devlist?devlist=0'
          })
        }, 1000)
      }
    }
  }
</script>

<style scoped>

  .boxSuccess{
    text-align: center;
  }

  .boxSuccess img{
    width: 200rpx;
    height:186rpx ;
    border: 1px solid transparent
  }
  .boxSuccess h3{
    font-weight: 500;
    color: #333333;
    margin-top: 40rpx;
    margin-bottom: 30rpx;
    font-size: 36rpx;
  }
  .boxSuccess p{
    color: #666666;
    font-size: 14px;
  }
  .boxSuccess h4{
    background: #01CDE8;
    height: 84rpx;
    line-height: 84rpx;
    width: 304rpx;
    color: #fff;
    font-size: 30rpx;
    box-shadow: 1px 1px 2px #01CDE8;
    border-radius: 40rpx;
    margin: 0 auto;
    margin-top: 70rpx;
  }
  .descript img{
      height: 272rpx;
    width: 496rpx;
    position: absolute;
    left: 50%;
    margin-left: -248rpx;
    bottom: 87rpx;
  }
     .linkPhone{
       width: 510rpx;
       height: 510rpx;
       position: absolute;
       left: 50%;
       top: 50%;
       margin-left: -255rpx;
       margin-top: -255rpx;
       box-shadow: 0 0 5rpx #ddd;
       text-align: center;
       background: #fff;
       border-radius: 20rpx;
     }
  .linkPhone img{
    width: 164rpx;
    height: 164rpx;
    position: absolute;
    left: 50%;
    margin-left: -82rpx;
    margin-top: -82rpx;
    top:0;
    border-radius: 50%;
    background: #fff;
  }
  .linkPhone h4{
    margin-top: 103rpx;
    font-size: 36rpx;
    color: #000;
  }
  .linkPhone p{
    width: 367rpx;
    margin: 0 auto;
    margin-bottom: 40rpx;
    margin-top: 22rpx;
    font-size: 24rpx;
  }
  .linkPhone span{
    display: block;
    width: 305rpx;
    height: 70rpx;
    margin: 0 auto;
    line-height:70rpx ;
    color: #333;
    margin-top: 20rpx;
    font-size: 28rpx;
  }
  .linkPhone span.link{
    background: #01cde8;
    border-radius: 40rpx;
    color: #fff;
  }
  .modalPage{
    width: 100%;
    height: 100%;
    background: rgba(0,0,0,0.3);
    position: absolute;
    left: 0;
    top: 0;
    z-index: 100000;
  }
</style>
