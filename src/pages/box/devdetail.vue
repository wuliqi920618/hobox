<template>
  <div class="page">
    <div class="weui-cells">
      <div class="weui-cell" @click="devedit" :data-item='devdata'>
        <div class="weui-cell__hd">
          <div class="pointDiv">
            <i class="iconfont" v-if="devdata.pid!=0" v-bind:class="{'icon-dev_android':devdata.dtype==0,'icon-dev_iphone':devdata.dtype==1,
          'icon-dev_pad':devdata.dtype==2,'icon-dev_pad1':devdata.dtype==3, 'icon-dev_laptop':devdata.dtype==4, 'icon-dev_laptop1':devdata.dtype==5,
          'icon-dev_taishiji':devdata.dtype==6, 'icon-dev_jiqiren':devdata.dtype==7,'icon-dev_home':devdata.dtype==8, 'icon-dev_tv':devdata.dtype==9,
          'iconcolor_green':devdata.color == 'green','iconcolor_grey':devdata.color == 'grey'}">
              <img v-if="devdata.block==1" src="/static/icon/onNet.png" class="position"
                   style="width: 32rpx;height: 32rpx"/>
            </i>
            <i slot="icon" v-else
               :class="['iconfont','icon-dev_unknow',{'iconcolor_grey':devdata.color == 'grey','iconcolor_orange': devdata.color == 'orange'}]">
              <img v-if="devdata.block==1" src="/static/icon/onNet.png" class="position"
                   style="width: 32rpx;height: 32rpx"/>
            </i>
          </div>
        </div>
        <div class="weui-cell__bd">
          <h4 class="weui-media-box__title" v-if="devdata.dname !=''">{{ devdata.dname }}</h4>
          <h4 class="weui-media-box__title" v-else-if="devdata.hostname !=''">{{ devdata.hostname }}</h4>
          <h4 class="weui-media-box__title" v-else-if="devdata.netbios !=''">{{ devdata.netbios }}</h4>
          <h4 class="weui-media-box__title" v-else-if="devdata.oui !=''">{{ devdata.oui }}</h4>
          <label class="weui-media-box__desc" style="font-size: 20rpx">最近更新：{{devdata.lastTimeStr}}</label>
          <label class="weui-media-box__desc" style="font-size: 20rpx">首次出现：{{devdata.firstTimeStr}}</label>
        </div>
        <div class="weui-cell__ft">
          <img src="../../../static/icon/bj_gray.png" alt="" class="bj" @click="personEdit">
        </div>
      </div>
    </div>

    <div class="weui-cells weui-cells_form" style="padding: 20rpx 0rpx">
      <div class="weui-cell">
        <div class="weui-cell__hd"><label class="weui-label">主机名称</label></div>
        <div class="weui-cell__bd">
          {{ devdata.hostname }}
        </div>
      </div>
      <div class="weui-cell">
        <div class="weui-cell__hd"><label class="weui-label">设备厂商</label></div>
        <div class="weui-cell__bd">
          {{ devdata.oui }}
        </div>
      </div>
      <div class="weui-cell">
        <div class="weui-cell__hd"><label class="weui-label">MAC地址</label></div>
        <div class="weui-cell__bd">
          {{ devdata.mac }}
        </div>
      </div>
      <div class="weui-cell">
        <div class="weui-cell__hd">
          <label class="weui-label">IP地址</label>
        </div>
        <div class="weui-cell__bd">
          {{ devdata.devip }}
        </div>
      </div>
    </div>

    <div class="weui-cells">
      <div class="weui-cell" >
        <div class="weui-cell__hd"><label class="weui-label">使用人</label></div>
        <div class="weui-cell__bd" v-if="person">
          <Avatar :imageurl="person.imageurl" iclass="d_p_avatar" online="true" borderline="true"></Avatar>
        </div>
        <div :class="['weui-cell__hd',{'weui-cell__ft_in-access':person}]">{{ person.nickname }}</div>
      </div>
    </div>

    <div class="weui-cells" style="border-bottom: 1px solid #eeeeee">
      <div class="weui-cell">
        <div class="weui-cell__hd" style="color: #00bcd4;">一键禁用</div>
        <div class="weui-cell__bd"></div>
        <div class="weui-cell__ft">
          <!--<switch v-if="devdata.block == 1" checked  @change = "switchChange"/>-->
          <!--<switch v-else  @change = "switchChange"/>-->
          <switch :checked="checkedSwitch" @change="switchChange"/>
        </div>
      </div>
    </div>
    <div class="planDiv">
      <div v-show="!checkedSwitch">
        <div v-if="plan.length > 0">
          <i-swipeout v-for="(p,index) in plan" :key="p.uid" operateWidth="120">
            <view slot="content">
              <i-cell title="限网计划">
                <label slot="footer" style="color: #ff9920;">{{p.ptypeStr}}&nbsp;&nbsp;&nbsp;{{p.starttimeStr}}~{{p.endtimeStr}}</label>
              </i-cell>
            </view>
            <view slot="button" class="i-swipeout-demo-button-group">
              <view class="i-swipeout-demo-button" style="width:60px;background: #ff8b03;" @click="editplan"
                    :data-item='p'><i class="iconfont icon-bianji" style="font-size: 28px"></i></view>
              <view class="i-swipeout-demo-button" style="width:60px;background: #ff5a5b;" @click="delPlan"
                    :data-item='p' :data-index="index"><i class="iconfont icon-shanchu" style="font-size: 28px"></i>
              </view>
            </view>
          </i-swipeout>
        </div>
        <div class="weui-cells weui-cells_after-title" v-else>
          <div class="weui-cell">
            <div class="weui-cell__bd" style="text-align: center;color: #999999">暂无限网计划</div>
          </div>
        </div>
        <div class="weui-cells weui-cells_after-title">
          <div class="weui-cell">
            <div class="weui-cell__bd" style="text-align: center">
              <button class="weui-btn mini-btn" plain="true" @click="addplan">新增计划</button>
            </div>
          </div>
        </div>
      </div>
    </div>


  </div>
</template>
<script>
  import Avatar from '../../components/avatar'

  export default {
    data () {
      return {
        devdata: {},
        person: {},
        plan: [],
        checkedSwitch: false,
        state: false
      }
    },
    components: {
      Avatar
    },
    onLoad () {
      wx.showNavigationBarLoading()
      setTimeout(_ => {
        wx.hideNavigationBarLoading()
      }, 500)
    },
    onShow () {
      this.devDetails()
    },
    methods: {
      devDetails () {
        let devdata = wx.getStorageSync('devdata')
        console.log(devdata)
        if (devdata.block == 1) {
          this.checkedSwitch = true
        } else {
          this.checkedSwitch = false
        }
        this.devdata = devdata
        this.devplan()
        if (devdata.person) {
          if (devdata.online) {
            devdata.person['online'] = true
          }
          this.person = devdata.person
          wx.setStorageSync('person', devdata.person)
        } else {
          this.person = false
        }
      },
      devplan () {
        this.$api.get('/plan/' + this.devdata.dmac, null, null, r => {
          let planList = r.data
          planList.forEach(function (v, k) {
            planList[k]['leftVal'] = 0
            planList[k]['starttimeStr'] = parseInt(v.starttime / 3600) + ':' + (Array(2).join('0') + parseInt((v.starttime % 3600) / 60)).slice(-2)
            planList[k]['endtimeStr'] = parseInt(v.endtime / 3600) + ':' + (Array(2).join('0') + parseInt((v.endtime % 3600) / 60)).slice(-2)
            switch (v.ptype) {
              case 0:
                planList[k]['ptypeStr'] = '每天'
                break
              case 1:
                planList[k]['ptypeStr'] = '周一至周五'
                break
              case 2:
                planList[k]['ptypeStr'] = '假日(周末)'
                break
            }
          })
          this.plan = planList
        })
      },
      devedit () {
        var devEditPid = this.devdata.pid
        wx.setStorageSync('devEditPid', devEditPid)
        wx.navigateTo({
          url: '/pages/box/devedit'
        })
      },
      addplan () {
        wx.navigateTo({
          url: '/pages/box/planadd?dmac=' + this.devdata.dmac
        })
      },
      editplan (e) {
        let item = e.currentTarget.dataset.item
        wx.navigateTo({
          url: '/pages/box/planadd?plan=' + JSON.stringify(item)
        })
      },
      delPlan (e) {
        let index = e.currentTarget.dataset.index
        let item = e.currentTarget.dataset.item
        this.$api.setLoadding(false)
        this.$api.delete('/plan/' + item.pid, null, null, r => {
          this.plan.splice(index, 1)
          this.$api.setLoadding(true)
          // 设置为未阻断状态，隐藏阻断wifi图标
          this.devdata.blocking = 2
          wx.showToast({
            title: '成功',
            icon: 'none',
            duration: 1500
          })
          this.devplan()
        })
      },
      personinfo () {
        wx.navigateTo({
          url: '/pages/box/persondetail'
        })
      },
      /*switchChange (e) {
        var _this=this
        let dmac = this.devdata.dmac
        let obj = {}
        if (e.mp.detail.value) {
          obj = {'block': 1, 'dmac': dmac}
          this.$api.put('/dev/' + dmac + '/block', obj, null, r => {
            // 由于后台和设备是异步请求需要1-2秒时间，所以前端会在请求后立刻显示阻断状态
            _this.devdata.blocking = 1
          })

        } else {
          obj = {'block': 2, 'dmac': dmac}
          this.$api.put('/dev/' + dmac + '/block', obj, null, r => {

          _this.devdata.blocking = 2
          })
        }
        wx.setStorageSync('devdata', this.devdata)
        wx.setStorageSync('block', obj)
      },*/
      switchChange (e) {

        if (this.state) {
          return
        }

        let dmac = this.devdata.dmac
        let obj = {}
        let sessionId = wx.getStorageSync('sessionId')
        let headers = Object.assign({}, {'content-type': 'application/json;charset=utf-8', 'Cookie': sessionId})
        var _this = this
        if (_this.checkedSwitch) {
          obj = {'block': 2, 'dmac': dmac}
        } else {
          obj = {'block': 1, 'dmac': dmac}
        }
        wx.request({
          url: 'https://www.hobox.com.cn:8082/api/test/app/dev/' + dmac + '/block',
          method: 'PUT',
          data: obj,
          header: headers,
          success: res => {
            if (res.data.statusCode === 200) {
              _this.checkedSwitch = !_this.checkedSwitch
            } else {
              _this.state = true
              _this.checkedSwitch = _this.checkedSwitch
              _this.state = false
              wx.showToast({
                title: res.data.message,
                icon: 'none',
                duration: 3000
              })
            }
            console.log(_this.checkedSwitch)
            _this.devdata.block = _this.checkedSwitch ? 1 : 2
          },
          fail: err => {
            wx.showToast({
              title: res.data.message,
              icon: 'none',
              duration: 3000
            })
          }
        })

        wx.setStorageSync('selectedDev', this.selected)
        wx.setStorageSync('block', obj)
      }
    }
  }
</script>

<style scoped>
  .weui-cells_form {
    font-size: 14px;
  }
  .weui-cells_form .weui-cell{
    padding: 5px 15px;
  }
  .weui-cells_form .weui-label{
    color: rgba(154,154,154,0.80);
  }
  .weui-cells {
    font-size: 14px;
  }
  .weui-cell:before {
    border-top: 0px;
  }
  .pointDiv {
    position: relative;
    width: 50px;
    height: 50px;
    text-align: center;
    display: block;
    overflow: visible;
    margin-right: 10px;
  }
  .pointDiv .iconfont{
    font-size: 80rpx;
  }
  .position {
    position:absolute;
    z-index:999;
    width: 16px;
    height: 16px;
    right: 0%;
    top:0%;
    border-radius:16px;
    height:16px;
    text-align:center;
    line-height:16px;
    font-size: 16px;
  }
  .weui-cells_form .weui-cell__bd{
    text-align: right;
  }
  .iconcolor_green{
    color: #00bcd4;
    font-size: 38px;
    line-height: 50px
  }
  .iconcolor_grey{
    color: #cccccc;
    font-size: 38px;
    line-height: 50px
  }
  .iconcolor_orange{
    color: #ff9920;
    font-size: 38px;
    line-height: 50px
  }
  .linebackgroud{
    background-color: #15C2BC !important;
  }
  .pidbackgroud{
    background-color: #ffd059 !important;
  }
  .mini-btn{
    display: inherit;
    height: 60rpx !important;
    line-height: 60rpx !important;
    width: 200rpx !important;
    border: 1px solid #00bcd4;
    color: #00bcd4;
    font-size: 28rpx;
  }

  .i-swipeout-demo-button-group{
    height:100%;
    width:100%;
  }
  .i-swipeout-demo-button{
    width:70px;
    float:left;
    display: flex;
    height:100%;
    justify-content: center;
    background:#2d8cf0;
    color:#fff;
    align-items:center;
  }
  .weui-media-box__title{
    max-width: 450rpx;
  }
  .planDiv{
    min-height: 212rpx;
    margin-bottom:20rpx;
    background:#fff;
  }
  .bj{
    width:36rpx;
    height:36rpx;
    position: absolute;
    right:30rpx;
    top: 50%;
    margin-top:-18rpx
  }

</style>
