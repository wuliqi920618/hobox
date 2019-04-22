<template>
  <div class="page">

    <div class="weui-cells">
      <div class="weui-cell">
        <div class="weui-cell__hd">头像</div>
        <div class="weui-cell__bd" style="text-align: right;margin-right: 40rpx" @click="userImg">
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
          <input class="weui-input" type="text" placeholder="请输入姓名" v-model="person.nickname" >
        </div>
      </div>
    </div>


    <div class="button-sp-area">
      <button class="weui-btn" style="background: #00bcd4;color: #ffffff" @click="add">确定</button>
    </div>

  </div>
</template>

<script>
    import Avatar from '../../components/avatar'
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
      onReady () {
        this.person.nickname = ''
        this.person.uicon = 0
        this.person.imageurl = this.$api.ImgName(this.person.uicon)
      },
      onShow () {
        let uicon = wx.getStorageSync('uicon')
        if (typeof uicon === 'number') {
          this.person.uicon = uicon
        }
        this.person['imageurl'] = this.$api.ImgName(uicon)
      },
      methods: {
        add () {
          this.$api.post('/person', this.person, null, r => {
            wx.removeStorageSync('uicon')
            wx.showToast({
              title: '已完成',
              icon: 'success',
              duration: 1500
            })
            wx.navigateBack()
          })
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

  .myavatar {
    width: 84rpx;
    height: 84rpx;
  }

  .button-sp-area {
    margin: 1.17647059em 15px 2em;
  }
</style>
