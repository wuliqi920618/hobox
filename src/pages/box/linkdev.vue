<template>
  <div class="page">
    <div style="border-top: 40rpx solid #eee">
      <h3>为该人员添加设备</h3>
      <div class="weui-grids">
        <block>
          <div v-for="(item,index) in devList" :key="item.did" class="weui-grid">
            <div class="weui-grid__icon">
              <Avatar :pid="item.pid" :online="item.online" :dtype="item.dtype" :borderline="selected==item.did" iclass="dev_avatar"></Avatar>
              <p class="weui-grid__label" v-if="item.dname!=''" style="color: #999999;margin-top: 0px;font-size: 22rpx">{{ item.dname }}</p>
              <p class="weui-grid__label" v-else-if="item.hostname!=''" style="color: #999999;margin-top: 0px;font-size: 22rpx">{{ item.hostname }}</p>
              <p class="weui-grid__label" v-else-if="item.netbios!=''" style="color: #999999;margin-top: 0px;font-size: 22rpx">{{ item.netbios }}</p>
              <p class="weui-grid__label" v-else-if="item.oui!=''" style="color: #999999;margin-top: 0px;font-size: 22rpx">{{ item.oui }}</p>
              <span class="weui-badge" @click="deleteDev(item,index)">-</span>
            </div>
          </div>
        </block>

        <block>
          <div class="weui-grid" @click="addDev" v-show="addBtn">
            <i class="iconfont icon-add" style="" id="icon-add"></i>
            <p>添加</p>
          </div>
        </block>
      </div>
    </div>

    <div class="button-sp-area">
      <p class="conform" @click="add">提交</p>
      <p class="cancel" @click="cancal">取消</p>
    </div>
    <i-toast id="toast" />


  </div>
</template>

<script>
  const { $Toast } = require('../../../static/iView/base/index');

  export default {
    data () {
      return {
        visible4: false,
        devList: [],
        addBtn: true,
        devMacList: []
      }
    },

    onUnload () {
      wx.removeStorageSync('zxc')
      this.devList = []
    },
    onShow () {
      this.getdev()
    },
    methods: {
      addDev () {
        wx.navigateTo({
          url: '/pages/box/devlist?devlist=1'
        })
      },
      add () {
        var addPerson = wx.getStorageSync('asasas')
        this.person = addPerson
        var _this = this
        var len = _this.devList.length
        if (_this.devList.length === 0) {
          $Toast({
            content: '请至少选择一个设备',
            type: 'warning'
          })
        } else {
          this.$api.post('/person', _this.person, null, r => {
            wx.removeStorageSync('uicon')
            for (var i = 0; i < len; i++) {
              var param = {
                pid: r.data.uid
              }
              _this.$api.put('/dev/' + _this.devList[i].dmac, param, null, r => {

              })
            }
            wx.showToast({
              title: '已完成',
              icon: 'success',
              duration: 1500
            })
            setTimeout(_ => {
              wx.switchTab({
                url: '/pages/box/home'
              })
            }, 1000)
            wx.removeStorageSync('ddddddd')
          })
        }
      },

      cancal () {
        wx.navigateBack()
      },
      deleteDev (index) {
        this.devList.splice(index, 1)
      },
      getdev () {
        var dev = wx.getStorageSync('zxc')
        console.log(dev)
        var _this = this
        if (dev) {
          var dmac = dev.dmac
          if (JSON.stringify(_this.devMacList).indexOf(JSON.stringify(dmac)) !== -1) {
            return
          } else {
            _this.devList.splice(_this.devList.length, 0, dev)
            _this.devMacList.splice(_this.devMacList.length, 0, dev.dmac)
          }
          console.log(_this.devList)
        }
        this.addBtn = this.devList.length >= 3 ? false : true
      }

    }
  }
</script>

<style scoped>

  .weui-cell__hd {
    font-weight: 400;
  }

  .weui-cells {
    margin-top: 0;
  }

  .page .weui-cells:first-child {
    height: 134rpx;
    line-height: 94 rpx;;
    border-bottom: 1px solid #d1d1d1;
    border-top: 40rpx solid #999;
  }

  .page .weui-cells:first-child + .weui-cells {
    height: 98rpx;
    border-bottom: 40rpx solid #999;
  }

  button {
    width: 533rpx;
    height: 90rpx;
    line-height: 90rpx;
    text-align: center;
    margin: 0 auto;
    border-radius: 60rpx;
    margin-top: 60rpx;
  }

  .conform {
    width: 533rpx;
    height: 84rpx;
    border-radius: 40rpx;
    background: #01CDE8;
    box-shadow: 1px 1px 2px #01CDE8;
    color: #fff;
    font-size: 32rpx;
    margin: 0 auto;
    line-height: 84rpx;
    text-align: center;
    margin-top: 222rpx;
    margin-bottom: 50rpx;
  }

  .cancel {
    width: 533rpx;
    height: 84rpx;
    color: #666666;
    margin: 0 auto;
    font-size: 32rpx;
    line-height: 84rpx;
    text-align: center;
  }

  .button-sp-area {
    margin: 1.17647059em 15px 2em;
  }

  h3 {
    color: #222;
    margin: 32rpx 0 53rpx 26rpx;
    font-weight: 500
  }

  .weui-grids {
    margin-left: 45rpx;
    border: none;
  }

  .weui-grid {
    width: 133rpx;
    height: 153rpx;
    margin: 0rpx 20rpx 20rpx 0rpx;
    background: #ddd;
    border: none;
  }

  .weui-grid__icon {
    display: inline-block;
    width: 96rpx;
    height: 96rpx;
    position: absolute;
    left: 50%;
    top: 10rpx;
    margin-left: -48rpx;
  }

  .weui-grid__label {
    display: inline-block;
    width: 96rpx;
    position: absolute;
    left: 50%;
    margin-left: -48rpx;
    bottom: 0rpx;
    color: #999999;
  }


  .weui-badge {
    position: absolute;
    top: 8rpx;
    right: 8rpx;
  }
  .weui-grid__icon i{
    display: inline-block;
    width: 100%;
    height: 100%;
    font-size: 72rpx;
  }
  #icon-add{
    font-size: 36rpx;
    color: #999;
    margin-bottom: 14rpx;
  }
  #icon-add+p{
    font-size: 22rpx;
    color: #999;
  }
</style>
