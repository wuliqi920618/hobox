<template>
  <div class="page" style="border: 1rpx solid transparent">
    <div class="weui-cells">
      <div class="weui-cell">
        <div class="weui-cell__hd">头像</div>
        <div class="weui-cell__bd" style="text-align: right;margin-right: 40rpx" @click="userImg">
          <Avatar online="true" iclass="myavatar" borderline="true" :imageurl="person.imageurl"></Avatar>
        </div>
        <div class="weui-cell__ft weui-cell__ft_in-access"></div>
      </div>
    </div>
    <div class="weui-cells">
      <div class="weui-cell">
        <div class="weui-cell__hd">人员名称</div>
        <div class="weui-cell__bd" style="text-align: right">
          <input class="weui-input" type="text" placeholder="请输入姓名" v-model="person.nickname">
        </div>
      </div>
    </div>

    <div class="weui-cells" style="min-height: 220rpx">
      <h3>我的设备</h3>
      <div class="weui-grids" style="min-height: 160rpx">

        <div v-for="(item,index) in devList" :key="item.did" class="weui-grid">
          <div class="weui-grid__icon">
            <Avatar :pid="item.pid" :online="item.online" :dtype="item.dtype" :borderline="selected==item.did"
                    iclass="dev_avatar"></Avatar>
            <p class="weui-grid__label" v-if="item.dname!=''" style="color: #999999;margin-top: 0px;font-size: 22rpx">{{
              item.dname }}</p>
            <p class="weui-grid__label" v-else-if="item.hostname!=''"
               style="color: #999999;margin-top: 0px;font-size: 22rpx">{{ item.hostname }}</p>
            <p class="weui-grid__label" v-else-if="item.netbios!=''"
               style="color: #999999;margin-top: 0px;font-size: 22rpx">{{ item.netbios }}</p>
            <p class="weui-grid__label" v-else-if="item.oui!=''"
               style="color: #999999;margin-top: 0px;font-size: 22rpx">{{ item.oui }}</p>
            <span class="weui-badge" @click="deleteDev(item,index)">-</span>


          </div>


        </div>


        <div class="weui-grid" @click="addDev" v-show="addBtn">
          <i class="iconfont icon-add" style="" id="icon-add"></i>
          <p>添加</p>
        </div>

      </div>
    </div>
    <i-modal   :visible="visible"  :show-cancel="false"
               :show-ok="false" i-class="modal" >
      <h4 class="modalTitle">确认删除</h4>
      <p>是否要删除 <span class="usernickname">[{{person.nickname}}] ？</span></p>
      <span class="modalBtn changeFocusBtn" @click="deletePerson">确定删除</span><span class="modalBtn cancelBtn" @click="cancel">取消</span>
    </i-modal>

    <div class="button-sp-area">
      <p class="update" @click="update">提交</p>
      <p class="delete" @click="confirmDel">删除人员</p>
    </div>

  </div>
</template>

<script>
  import Avatar from '../../components/avatar'

  export default {
    data () {
      return {
        person: {},
        devList: [],
        addBtn: true,
        devMacList: [],
        visible: false
      }
    },
    components: {
      Avatar
    },
    onReady () {
      wx.removeStorageSync('addDev')
    },
    onLoad () {
      this.person = wx.getStorageSync('person')
      console.log(this.person)
      this.getDev()
      this.devList = []
      this.devMacList = []
    },
    onShow () {
      let uicon = wx.getStorageSync('uicon')
      if (typeof uicon === 'number') {
        wx.removeStorageSync('uicon')
        this.person.uicon = uicon
        this.person['imageurl'] = this.$api.ImgName(uicon)
      }
      var dev = wx.getStorageSync('addDev')
      var _this = this
      if (dev !== '') {
        var dmac = dev.dmac
        if (JSON.stringify(_this.devMacList).indexOf(JSON.stringify(dmac)) !== -1) {
          return
        } else {
          _this.devList.splice(_this.devList.length, 0, dev)
          _this.devMacList.splice(_this.devMacList.length, 0, dev.dmac)
          var param = {
            pid: _this.person.uid
          }
          _this.$api.put('/dev/' + dev.dmac, param, null, r => {
          })
        }
      }
      _this.addBtn = this.devList.length >= 3 ? false : true
    },
    onUnload () {
      wx.removeStorageSync('addDev')
    },
    onHide () {
      wx.removeStorageSync('addDev')
    },
    methods: {

      getDev () {
        var _this = this
        let uid = this.person['uid']
        if (uid) {
          this.$api.get('/dev', {'pid': uid, 'order': 'lasttime'}, null, r => {
            var devArr = r.data
            if (devArr.length > 0) {
              // _this.devList = r.data
              devArr.forEach(function (item) {
                if (JSON.stringify(_this.devMacList).indexOf(JSON.stringify(item.dmac)) !== -1) {

                } else {
                  _this.devList.splice(_this.devList.length, 0, item)
                  _this.devMacList.splice(_this.devMacList.length, 0, item.dmac)
                }
              })
            }
            _this.addBtn = this.devList.length >= 3 ? false : true
          })

        }
      },
      addDev () {
        wx.navigateTo({
          url: '/pages/box/devlist?devlist=3'
        })
      },
      deleteDev (item, index) {
        this.devList.splice(index, 1)
        this.devMacList.splice(index, 1)
        this.addBtn = this.devList.length >= 3 ? false : true
        var param = {
          pid: 0
        }
        this.$api.put('/dev/' + item.dmac, param, null, r => {
        })
      },
      update () {
        this.$api.put('/person/' + this.person.uid, this.person, null, r => {
          wx.setStorageSync('person', this.person)
          wx.showToast({
            title: '已完成',
            icon: 'success',
            duration: 1500
          })
          setTimeout(function () {
            wx.navigateBack()
          }, 1500)
        })
        wx.setStorageSync('personEdit', this.person)
      },
      confirmDel () {
        this.visible = true
      },
      cancel(){
        this.visible = false
      },
      deletePerson () {
        console.log(this.person)
        this.$api.delete('/person/' + this.person.uid, null, null, r => {
          setTimeout(_ => {
            wx.switchTab({
              url: '/pages/box/home'
            })
          }, 1000)
        })
      },
      userImg () {
        wx.setStorageSync('uicon', this.person.uicon)
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
  .weui-grids {
    margin-left: 45rpx;
    border: none;
    padding-bottom: 30rpx;
  }

  .weui-grid {
    width: 133rpx;
    height: 153rpx;
    margin: 0rpx 20rpx 20rpx 0rpx;
    background: #f0f0f0;
    border: none;
    border-radius: 10rpx;
  }

  .weui-grid__icon {
    display: inline-block;
    width: 96rpx;
    height: 96rpx;
    position: absolute;
    left: 50%;
    top: 20rpx;
    margin-left: -48rpx;
  }

  .weui-grid__label {
    display: inline-block;
    width: 96rpx;
    position: absolute;
    left: 50%;
    margin-left: -48rpx;
    bottom: -20rpx;
    color: #999999;
  }


  .weui-badge {
    position: absolute;
    top: -5rpx;
    right: -5rpx;
  }
  .weui-grid__icon i{
    display: inline-block;
    width: 100%;
    height: 100%;
    font-size: 72rpx;
  }
  h3{
    display: block;
    width: 100%;
    padding: 40rpx 0 30rpx 30rpx;
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
  .update{
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
  .delete{
    width: 180rpx;
    height: 84rpx;
    line-height: 84rpx;
    text-align: center;
    background: transparent;
    margin: 0 auto;
    border-radius: 40rpx;
    margin-top: 60rpx;
    border: 1rpx solid #999;
    color: #999;
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
  .modalBtn{
    display: inline-block;
    width: 160rpx;
    height: 60rpx;
    border-radius: 40rpx;
    text-align: center;
    line-height: 60rpx;
    margin-top: 40rpx;
  }
  .usernickname{
    color: #00BCD4;
  }
</style>
