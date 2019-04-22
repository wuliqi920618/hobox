<template>
  <div class="page">
    <div class="weui-cells ">
      <div class="weui-cell">
        <div class="weui-cell__hd">头像</div>
        <div class="weui-cell__bd" style="text-align: right;margin-right: 40rpx;color: #999999" @click="userImg">
          <Avatar online="true" iclass="myavatar" borderline="true" :imageurl="person.imageurl">
          </Avatar>
        </div>
        <div class="weui-cell__ft weui-cell__ft_in-access"></div>
      </div>
    </div>
    <div class="weui-cells">
      <div class="weui-cell">
        <div class="weui-cell__hd">人员名称</div>
        <div class="weui-cell__bd" style="text-align: right">
          <input class="weui-input" type="text" placeholder="请输入人员名称" v-model="person.nickname">
        </div>
      </div>
    </div>


    <div class="button-sp-area">
      <p class="next" style="" @click="add">新增</p>
    </div>
    <i-toast id="toast"/>

  </div>
</template>

<script>
  import Avatar from '../../components/avatar'

  const {$Toast} = require('../../../static/iView/base/index')
  export default {
    data () {
      return {
        person: {
          nickname: '',
          uicon: 0,
          imageurl: ''
        }
      }
    },
    components: {
      Avatar
    },
    onUnload () {
      wx.removeStorageSync('asasas')
      this.person = {
        nickname: '',
        uicon: 0,
        imageurl: ''
      }
      this.person.imageurl = this.$api.ImgName(this.person.uicon)
    },
    onReady () {
      this.person = {
        nickname: '',
        uicon: 0,
        imageurl: ''
      }
      this.person.imageurl = this.$api.ImgName(this.person.uicon)
    },
    onShow () {
      var _this = this
      let uicon = wx.getStorageSync('uicon')

      if (typeof uicon === 'number') {
        console.log(uicon)
        _this.person.uicon = uicon
        _this.person['imageurl'] = _this.$api.ImgName(uicon)
      } else {
        this.showImg = true
      }
    },
    methods: {
      add () {
        var _this = this
        wx.setStorageSync('asasas', _this.person)
        if (_this.person.nickname) {
          this.$api.post('/person', _this.person, null, r => {
            wx.setStorageSync('newPerson', r.data)
          })
          wx.showToast({
            title: '新增成功',
            icon: 'success',
            duration: 800
          })
          setTimeout(function () {
            wx.navigateTo({
              url: '/pages/box/adddev'
            })
          }, 800)
        } else {
          $Toast({
            content: '请输入人员名称',
            type: 'warning'
          })
        }
      },
      userImg () {
        wx.navigateTo({
          url: '/pages/box/personimg'
        })
      }
    }
  }
</script>

<style scoped>

  .weui-cell__hd{
    font-weight: 400;
  }

  .weui-cells{
    margin-top: 0;
  }
  .page .weui-cells:first-child{
    height: 134rpx;
    line-height: 94rpx;;
    border-bottom: 1px solid #d1d1d1;
    border-top: 40rpx solid #eee;
  }
  .page .weui-cells:first-child+.weui-cells{
    height:98rpx;
    border-bottom: 40rpx solid #eee;
  }
.next{
  width: 533rpx;
  height: 84rpx;
  line-height: 84rpx;
  text-align: center;
  background: #01CDE8;
  box-shadow: 1px 2px 6px #01CDE8;
  margin: 0 auto;
  border-radius: 40rpx;
  margin-top: 60rpx;
  color: #ffffff
}
  .button-sp-area {
    margin: 1.17647059em 15px 2em;
  }
</style>
