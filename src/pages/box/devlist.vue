<template>
  <div class="page">
    <div class="weui-cells">
      <div class="weui-cell weui-cell_access" v-for="(dev,index) in devHasPid" @click="devdetail(index,'1')" :key="dev.did">
        <div class="weui-cell__hd">
          <span>
            <i class="iconfont" v-bind:class="{'icon-dev_android':dev.dtype==0,'icon-dev_iphone':dev.dtype==1,
                'icon-dev_pad':dev.dtype==2,'icon-dev_pad1':dev.dtype==3, 'icon-dev_laptop':dev.dtype==4, 'icon-dev_laptop1':dev.dtype==5,
                'icon-dev_taishiji':dev.dtype==6, 'icon-dev_jiqiren':dev.dtype==7,'icon-dev_home':dev.dtype==8, 'icon-dev_tv':dev.dtype==9,
                    'iconcolor_green':dev.color == 'green','iconcolor_grey':dev.color == 'grey'}"></i>
            <img v-if="dev.block==1" src="/static/icon/onNet.png" class="position" />
          </span>
        </div>
        <div class="weui-cell__bd">
          <div class="weui-media-box__title">
            <div class="weui-flex__item">
              <p class="texthiden" v-if="dev.dname !=''">{{ dev.dname }}</p>
              <p class="texthiden" v-else-if="dev.hostname !=''">{{ dev.hostname }}</p>
              <p class="texthiden" v-else-if="dev.netbios !=''">{{ dev.netbios }}</p>
              <p class="texthiden" v-else-if="dev.oui !=''">{{ dev.oui }}</p>
            </div>
          </div>
          <div class="weui-media-box__desc">
            <div class="weui-flex">
              <div class="weui-flex__item">
                <p>{{ dev.mac }}</p>
              </div>
              <!--<div class="weui-flex__item">-->
                <!--<p>{{dev.lastTimeStr}}</p>-->
              <!--</div>-->
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="weui-cells" style="margin-top: 15px">
      <div class="weui-cell weui-cell_access" v-for="(item,index) in devNoPid" @click="devdetail(index,'0')" :key="item.did">
        <div class="weui-cell__hd">
        <span>
          <i class="iconfont icon-dev_unknow" v-bind:class="{'iconcolor_grey':item.color == 'grey','iconcolor_orange': item.color == 'orange'}"></i>
          <img v-if="item.blocking==1" src="/static/icon/onNet.png" class="position" />
        </span>
        </div>
        <div class="weui-cell__bd">
          <div class="weui-media-box__title">
            <div class="weui-flex__item">
              <p class="texthiden" v-if="item.dname !=''">{{ item.dname }}</p>
              <p class="texthiden" v-else-if="item.hostname !=''">{{ item.hostname }}</p>
              <p class="texthiden" v-else-if="item.netbios !=''">{{ item.netbios }}</p>
              <p class="texthiden" v-else-if="item.oui !=''">{{ item.oui }}</p>
            </div>
          </div>
          <div class="weui-media-box__desc">
            <div class="weui-flex">
              <div class="weui-flex__item">
                <p>{{ item.mac }}</p>
              </div>
              <!--<div class="weui-flex__item">-->
                <!--<p>{{item.lastTimeStr}}</p>-->
              <!--</div>-->
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        devHasPid: [],
        devNoPid: [],
        block: [],
        type: '',
        devIndex: '',
        devListParam: {}
      }
    },
    onLoad (e) {
      console.log(e)
      this.devListParam = e
    },

    onShow () {
      wx.showNavigationBarLoading()
      this.getData()
      setTimeout(_ => {
        wx.hideNavigationBarLoading()
      }, 1000)
    },
    methods: {
      getData () {
        this.$api.get('/dev', {'online': 0, 'order': 'lasttime'}, null, r => {
          let online = []
          let offline = []
          let nopidOnline = []
          let nopidOffline = []
          let _this = this
          r.data.forEach(function (v, k) {
            v['mac'] = _this.$api.ToMac(v.dmac)
            v['firstTimeStr'] = _this.$api.formatDate('yyyy-MM-dd hh:mm:ss', new Date(v.firsttime * 1000))
            v['lastTimeStr'] = _this.$api.formatDate('yyyy-MM-dd hh:mm:ss', new Date(v.lasttime * 1000))
            if (v.pid > 0) {
              if (v.online) {
                v['color'] = 'green'
                online.push(v)
              } else {
                v['color'] = 'grey'
                offline.push(v)
              }
            }
            if (v.pid === 0) {
              if (v.online) {
                v['color'] = 'orange'
                nopidOnline.push(v)
              } else {
                v['color'] = 'grey'
                nopidOffline.push(v)
              }
            }
          })
          this.devHasPid = online.concat(offline)
          this.devNoPid = nopidOnline.concat(nopidOffline)
        })
      },
      devdetail (index, flag) {
        var listNum = this.devListParam.devlist
console.log(listNum)
        if (listNum == 0) {
          let dev = null
          if (flag === '0') {
            dev = this.devNoPid[index]
            this.type = 0
          } else if (flag === '1') {
            dev = this.devHasPid[index]
            this.type = 1
          }
          this.devIndex = index
          if (dev.person) {
            dev.person['imageurl'] = this.$api.ImgName(dev.person.uicon)
          }
          var user = wx.getStorageSync('user')
          var param = {
            pid: user.uid
          }
          console.log(dev.dmac)
          this.$api.put('/dev/' + dev.dmac, param, null, r => {
            wx.showToast({
              title: '操作成功',
              icon: 'success',
              duration: 1500
            })
            wx.switchTab({
              url: '/pages/box/home'
            })

          })
        } else if (listNum == 2) {
          let dev = null
          if (flag === '0') {
            dev = this.devNoPid[index]
            this.type = 0
          } else if (flag === '1') {
            dev = this.devHasPid[index]
            this.type = 1
          }
          this.devIndex = index
          if (dev.person) {
            dev.person['imageurl'] = this.$api.ImgName(dev.person.uicon)
          }
          this.$api.get('/person-cared', null, null, r => {
            console.log(r)
            var user = r.data

            var param = {
              pid: user.uid
            }
            console.log(dev.dmac)
            this.$api.put('/dev/' + dev.dmac, param, null, r => {
              wx.showToast({
                title: '操作成功',
                icon: 'success',
                duration: 1500
              })
              wx.switchTab({
                url: '/pages/box/home'
              })
            })
          })
        }else if (listNum == 3) {
          let dev = null
          if (flag === '0') {
            dev = this.devNoPid[index]
            this.type = 0
          } else if (flag === '1') {
            dev = this.devHasPid[index]
            this.type = 1
          }
          this.devIndex = index
          if (dev.person) {
            dev.person['imageurl'] = this.$api.ImgName(dev.person.uicon)
          }
          wx.setStorageSync('addDev', dev)
          wx.navigateBack()
        } else {
          let dev = null
          if (flag === '0') {
            dev = this.devNoPid[index]
            this.type = 0
          } else if (flag === '1') {
            dev = this.devHasPid[index]
            this.type = 1
          }
          this.devIndex = index
          if (dev.person) {
            dev.person['imageurl'] = this.$api.ImgName(dev.person.uicon)
          }
          wx.setStorageSync('zxc', dev)
          // wx.navigateTo({
          //   url: '/pages/box/linkdev'
          // })
          wx.navigateBack()
        }

      },
      async onPullDownRefresh () {
        wx.showNavigationBarLoading()
        this.getData()
        setTimeout(_ => {
          wx.hideNavigationBarLoading()
          wx.stopPullDownRefresh()
        }, 1500)
      }
    }
  }
</script>

<style scoped>
  .page{
    font-size: 14px;
  }
  .weui-cells{
    margin-top: 0px;
  }
  .weui-cells:before {
    border-top: 0px;
  }
  .weui-cells:after {
    border-bottom: 0px;
  }
  .weui-cell__hd>span {
    position: relative;
    width: 50px;
    height: 50px;
    margin: auto;
    display: block;
    text-align: center;
    overflow: visible;
    background-repeat: no-repeat;
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
  .iconcolor_green{
    color: #00bcd4;
    font-size: 35px;
    line-height: 50px
  }
  .iconcolor_grey{
    color: #cccccc;
    font-size: 35px;
    line-height: 50px
  }
  .iconcolor_orange{
    color: #ff9920;
    font-size: 35px;
    line-height: 50px
  }
  .weui-cell__ft{
    display: inline-block;
    height: 6px;
    width: 6px;
    border-width: 2px 2px 0 0;
    border-color: #c8c8cd;
    border-style: solid;
    -webkit-transform: matrix(.71,.71,-.71,.71,0,0);
    transform: matrix(.71,.71,-.71,.71,0,0);
    position: relative;
    top: -2px;
    position: absolute;
    top: 50%;
    margin-top: -4px;
    right: 20px;
  }
  .linebackgroud{
    background-color: #15C2BC !important;
  }
  .pidbackgroud{
    background-color: #ffd059 !important;
  }
  .texthiden{
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    max-width: 500rpx;
  }
</style>
