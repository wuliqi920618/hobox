<template>
  <div class="page" style="background: #fff;width: 100%;height: 100%;margin: 0;padding: 0;padding-top: 60rpx">
    <div class="boxFail">
      <img src="./../../../static/icon/fail.png" alt="">
      <h3>很遗憾该盒子已被绑定</h3>
      <p>由于系统默认第一个绑定盒子的人为该盒子的管理员，如果你想要加入需要向管理员提出请求</p>
      <h4 @click="join()">申请加入</h4>
      <h4 @click="cancel()" class="cancel">取消</h4>
    </div>
  </div>
</template>

<script>
  export default {
    data () {
      return {}
    },
    onLoad () {
    },
    onShow () {
    },
    methods: {
      join () {
        wx.navigateTo({
          url: '/pages/box/home'
        })
      },
      cancel () {
        wx.navigateTo({
          url: '/pages/box/home'
        })
      }
    }
  }
</script>

<style scoped>

  .boxFail{
    text-align: center;

  }

  .boxFail img{
    width: 200rpx;
    height:186rpx ;
    border: 1px solid green;
  }
  .boxFail h3{
    font-weight: 500;
    color: #333333;
    margin-top: 40rpx;
    margin-bottom: 40rpx;
    font-size: 36rpx;
  }
  .boxFail p{
    width: 85%;
    margin: 0 auto;
    color: #333333;
    font-size: 14px;
  }
  .boxFail h4{
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
  .boxFail .cancel{
    margin-top: 60rpx;
    color: #666666;
    background: transparent;
    box-shadow: 1px 1px 2px transparent;
  }

</style>
