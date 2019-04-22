<template>
  <div class="page">

    <div class="weui-cells">
      <radio-group @change="checkboxChange">
        <label class="weui-cell weui-check__label" v-for="item in box" :key="index">
          <radio class="weui-check" :value="item.bid" :checked="item.checked" />
          <div class="weui-cell__bd" style="margin-left: 0px;width: 80%">
            <h4 class="weui-media-box__title" style="width: 100%">路由器：{{item.essid}}</h4>
            <p class="weui-media-box__desc">路由器MAC地址：{{item.rmacStr}}</p>
            <p class="weui-media-box__desc">盒子MAC地址：{{item.bmacStr}}</p>
          </div>
          <div class="weui-cell__ft weui-check__hd_in-checkbox">
            <icon class="weui-icon-checkbox_circle" type="circle" size="23" v-if="!item.checked"></icon>
            <icon class="weui-icon-checkbox_success" type="success" size="23" v-if="item.checked"></icon>
          </div>
        </label>
      </radio-group>
    </div>
    <i-modal  :visible="visible"  :show-cancel="false"
               :show-ok="false" i-class="modal2" >
      <h3 class="modalTitle">切换家小盒</h3>
      <p>确定切换到该盒子吗</p>
      <span class="modalBtn changeFocusBtn" @click="changeBox">确认更换</span><span class="modalBtn cancelBtn" @click="cancel">取消</span>
    </i-modal>
    <div class="right-icon" @click="searchBox">
      <i class="iconfont icon-addcontacts" style="font-size: 90rpx;color: #15C2BC;"></i>
    </div>
  </div>
</template>

<script>
    export default {
      data () {
        return {
          bid: 0,
          box: [],
        visible:false,
          value:''
        }
      },
      onLoad () {
        this.getBox()
      },
      onShow () {
        this.bid = this.$root.$mp.query.bid
        this.visible=false
      },
      methods: {
        getBox () {
          this.$api.get('/box/all', null, null, r => {
            let _this = this
            let bid = parseInt(this.bid)
            let boxlist = []
            r.data.forEach(function (v, k) {
              if (bid === v.bid) {
                v['checked'] = true
              } else {
                v['checked'] = false
              }
              v['bmacStr'] = _this.$api.ToMac(v.bmac)
              v['rmacStr'] = _this.$api.ToMac(v.rmac)
              boxlist.push(v)
            })
            _this.box = boxlist
          })
        },
        checkboxChange (e) {
          this.visible=true
          console.log(e.mp.detail.value)
          this.value=e.mp.detail.value
          // let _this = this
          // wx.showModal({
          //   title: '切换家小盒',
          //   content: '确定切换到该盒子吗',
          //   confirmText: '确认',
          //   cancelText: '取消',
          //   success: function (res) {
          //     console.log(res)
          //     if (res.confirm) {
          //       let checkboxItems = _this.box
          //       let values = e.mp.detail.value
          //       for (var i = 0, lenI = checkboxItems.length; i < lenI; ++i) {
          //         checkboxItems[i].checked = false
          //         if (checkboxItems[i].bid === parseInt(values)) {
          //           checkboxItems[i].checked = true
          //           _this.setbox(checkboxItems[i])
          //         }
          //       }
          //       _this.box = checkboxItems
          //     }
          //   }
          // })
        },
        changeBox(e){
          var _this=this
            let checkboxItems = _this.box
            let values =this.value
            for (var i = 0, lenI = checkboxItems.length; i < lenI; ++i) {
              checkboxItems[i].checked = false
              if (checkboxItems[i].bid === parseInt(values)) {
                checkboxItems[i].checked = true
                _this.setbox(checkboxItems[i])
              }
            }
            _this.box = checkboxItems

        },
        cancel(){
          this.visible=false
        },
        setbox (box) {
          var _this=this
          wx.request({
            url: 'https://www.hobox.com.cn:8082/api/test/app/usrbox',
            data: {'bmac': box.bmac, 'rmac': box.rmac},
            method: 'POST',
            header: {
              'Cookie': wx.getStorageSync('sessionId')
            },
            success: res => {
              // 设置盒子后，更改session
              if (res.data.statusCode === 200) {
                wx.setStorageSync('sessionId', res.header['Set-Cookie'])
                let user = wx.getStorageSync('user')
                user.box = box
                user.bmac = box.bmac
                wx.setStorageSync('user', user)
                _this.visible=false
                wx.switchTab({
                  url: '/pages/box/home',
                  success (res) {
                    let page = getCurrentPages().pop()
                    if (page === undefined || page === null) {
                      return
                    }
                    page.onLoad()
                  }
                })
                wx.setStorageSync('wxbind', true)
              } else {
                wx.showToast({
                  title: res.data.message,
                  icon: 'none',
                  duration: 3000
                })
              }
            }
          })
        },
        searchBox(){
          wx.navigateTo({
            url: '/pages/box/searchBox'
          })
        }
      }
    }
</script>

<style scoped>
.weui-media-box__desc{
  padding: 4rpx 0;
}
.right-icon{
  position: fixed;
  right: 20px;
  bottom: 20px;
  z-index: 999;
}
.modalBtn{
  display: inline-block;
  width: 160rpx;
  height: 60rpx;
  border-radius: 40rpx;
  text-align: center;
  line-height: 60rpx;
  margin-top: 40rpx;
}


.changeFocusBtn {
  background: #00BCD4;
  color: #fff;
  float: left;
  margin-left: 55rpx;
  margin-top: 40rpx;
  border: 1rpx solid #00BCD4;;
}
.cancelBtn{
  border: 1rpx solid #999;
  color: #999;
  float: right;
  margin-right: 55rpx;
  margin-top: 40rpx;
}
.modalTitle{
  font-weight: 600;
  font-size: 30rpx;
  margin: 10px 0;
  color: #333333;
}
.modalTitle~p{
  width: 90%;
  margin: 0 auto;
}
</style>
