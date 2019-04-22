<template>
  <div class="page" v-if="show==1" style="background: #fff">
    <div class="person">
      <div class="switch_group">
        <span style="margin-right: 18rpx;color: #fff">学习模式</span>
        <i-switch :value="switch1" size="middle" @click="changeSwitch" slot="footer">
          <view slot="open" style="color: #fff">开</view>
          <view slot="close">关</view>
        </i-switch>
      </div>
      <img v-show="switch1" class="img_bg" src="../../../static/icon/study02.jpg" alt="">
      <img v-show="!switch1" class="img_bg" src="../../../static/icon/study01.jpg" alt="">
      <img :src=person.imageurl alt="" class="img_person" style="background: #fff">
      <h4 class="name"><span class="fouce_name">{{person.nickname}}</span><span class="change_focus"
                                                                                @click="carePerson">切换关注人</span></h4>
    </div>
    <div class="dev">
      <div class="weui-grids" v-show="showDev">
        <block v-for="(item, index) in dev" :key="index">
          <div class="weui-grid" @click="getData(index)">
            <div :class="[index === devNum? 'active':'noactive']">


              <div class="weui-grid__icon">
                <div :class="['pointDev',{'dev_online':item.online,'dev_offline': !item.online}]">
                  <i class="iconfont iconcolor" v-bind:class="{'icon-dev_android':item.dtype==0,'icon-dev_iphone':item.dtype==1,
                'icon-dev_pad':item.dtype==2,'icon-dev_pad1':item.dtype==3, 'icon-dev_laptop':item.dtype==4, 'icon-dev_laptop1':item.dtype==5,
                'icon-dev_taishiji':item.dtype==6, 'icon-dev_jiqiren':item.dtype==7,'icon-dev_home':item.dtype==8, 'icon-dev_tv':item.dtype==9}"></i>
                </div>
              </div>
              <p v-if="item.dname!=''" class="weui-grid__label" style="color: #999999;margin-top: 0px;font-size: 22rpx">
                {{ item.dname }}</p>
              <p v-else-if="item.hostname!=''" class="weui-grid__label"
                 style="color: #999999;margin-top: 0px;font-size: 22rpx">{{ item.hostname }}</p>
              <p v-else-if="item.netbios!=''" class="weui-grid__label"
                 style="color: #999999;margin-top: 0px;font-size: 22rpx">{{ item.netbios }}</p>
              <p v-else-if="item.oui!=''" class="weui-grid__label"
                 style="color: #999999;margin-top: 0px;font-size: 22rpx">{{ item.oui }}</p>
            </div>
            <img v-if="item.block==1" src="/static/icon/onNet.png" class="position" style="width: 32rpx;height: 32rpx"/>
          </div>
        </block>

      </div>
      <p class="noDev" v-show="!showDev" style="text-align: center" @click="adddev()">暂无设备,请点击添加设备</p>
    </div>
    <div class="main">
      <ul>
        <li :class="{tab_active:tab_bol == 0}" @click="onChange(0)">今日统计</li>
        <li :class="{tab_active:tab_bol == 1}" @click="onChange(1)">昨日统计</li>
      </ul>
      <div v-show="!showDev">
        <p class="noDev">暂无设备,暂无数据</p>
      </div>
      <div class="echarts_group1" v-show="showDev">
        <div class="echart_detail" style="height: 400rpx;margin-top: 40rpx">
          <div class="weui-grid__label" style="color: #999999;margin-top: 0px;font-size: 22rpx;text-align: left">
            当日在线总时长：{{ onlineS }}
          </div>
          <i-progress :percent="percent_onlineS" status="normal" hide-info></i-progress>
          <div class="weui-grid__label" style="color: #999999;margin-top: 0px;font-size: 22rpx;text-align: left">
            QQ/微信在线时长：{{ QQ_WECAT }}
          </div>
          <i-progress :percent="percent_QQ_WECAT" status="video" hide-info></i-progress>

          <div class="weui-grid__label" style="color: #999999;margin-top: 0px;font-size: 22rpx;text-align: left">
            学习在线时长：{{ study }}
          </div>
          <i-progress :percent="percent_study" status="success" hide-info id="studyProgress"></i-progress>

          <div class="weui-grid__label" style="color: #999999;margin-top: 0px;font-size: 22rpx;text-align: left">
            游戏在线时长：{{ game }}
          </div>
          <i-progress :percent="percent_game" status="wrong" hide-info></i-progress>
          <div class="weui-grid__label" style="color: #999999;margin-top: 0px;font-size: 22rpx;text-align: left">
            视频在线时长：{{ video }}
          </div>
          <i-progress :percent="percent_video" status="purple" hide-info></i-progress>
        </div>
        <div class="title">
          <div style="background:#19be6b;height: 22rpx;width: 22rpx;margin: 18rpx 4rpx"></div>
          <div>学习</div>
          <div style="background: #ed3f14;height: 22rpx;width: 22rpx;margin: 18rpx 4rpx;margin-left: 20rpx"></div>
          <div>游戏</div>
          <div style="background: #800080;height: 22rpx;width: 22rpx;margin: 18rpx 4rpx;margin-left: 20rpx"></div>
          <div>视频</div>
          <div style="background: #2db7f5;height: 22rpx;width: 22rpx;margin: 18rpx 4rpx;margin-left: 20rpx"></div>
          <div>其他</div>
        </div>
        <div class="canvas_title_div">
          <span class="canvas_title">凌晨0点</span>
          <span class="canvas_title">早上6点</span>
          <span class="canvas_title">中午12点</span>
          <span class="canvas_title">下午6点</span>
        </div>
        <div class="echart_detail" style="margin-top:80rpx;height: 328rpx;position: relative;width: 90%;margin-bottom: 100rpx">
          <!--<div @click="chartDetail()" style="width: 100%; height: 100%;position: absolute;top: 0;left: 0;z-index: 1000000000000000000000000000000;background: red">-->
          <!--</div>-->


          <canvas style="width: 25%; height:100%;position: absolute;left: 0;top: 0;background: #f7f7f7;border-right: 1rpx dashed #ddd;border-left: 1rpx dashed #ddd" canvas-id="firstCanvas"
                  >

          </canvas>
          <canvas style="width: 25%; height:100%;position: absolute;left: 25.5%;top: 0;background: #ffffff;border-right: 1rpx dashed #ddd" canvas-id="secondCanvas"
                 ></canvas>
          <canvas style="width: 25%; height:100%;position: absolute;left: 50.7%;top: 0;background: #ffffff" canvas-id="thirdCanvas"
                   ></canvas>
          <canvas style="width: 25%; height:100%;position: absolute;left: 75%;top: 0;background: #f7f7f7;border-left: 1rpx dashed #ddd;border-right: 1rpx dashed #ddd" canvas-id="fourthCanvas"
                  ></canvas>
          <mpvue-echarts :echarts="echarts" :onInit="handleInit" ref="echarts" @touchstart="chartDetail()"/>
        </div>

      </div>
      <div class="weui-cells" style="border-bottom: 1px solid #eeeeee">
        <div class="weui-cell">
          <div class="weui-cell__hd" style="color: #00bcd4;">一键禁用</div>
          <div class="weui-cell__bd"></div>
          <div class="weui-cell__ft">
            <switch :checked="checkedSwitch" @change="switchChange"/>
            <!--<switch v-else  @change = "switchChange"/>-->
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
              <div class="weui-cell__bd" style="text-align: center;">
                <button class="weui-btn mini-btn" plain="true" @click="addplan">新增计划</button>
              </div>
            </div>
          </div>
        </div>

      </div>

    </div>
  </div>
  <div v-else-if="show==2"
       style="padding-top: 30rpx;background: -webkit-linear-gradient(rgba(1,205,232,0.7) 0%,  #fff 40%);background: -o-linear-gradient(rgba(1,205,232,0.7) 0%,  #fff 40%);     background: -moz-linear-gradient(rgba(1,205,232,0.7) 0%,  #fff 40%);background: linear-gradient(rgba(1,205,232,0.7) 0%, #fff 40%);">
    <div class="welcome">
      <h4>欢迎使用家小盒</h4>
      <p>添加您关注人员，随时掌握他的网络使用数据</p>
      <span @click="find()"><i class="iconfont icon-add" style=""></i>添加关注人</span>
    </div>
    <div class="guide" @click="guide()">
      <h3>家小盒新人引导</h3>
      <p>Hello,主人初次见面，快来看看</p>
      <p>我有哪些功能吧~~</p>
      <img src="../../../static/icon/logo.png">
    </div>
    <div class="buy">
      <h3>购买家小盒</h3>
      <img src="../../../static/icon/banner.jpg">
    </div>
    <p class="share"><i class="iconfont icon-add" style=""></i>分享给好友</p>
  </div>
  <div v-else-if="show==0"></div>
</template>

<script>
  import * as echarts from 'echarts'
  import mpvueEcharts from 'mpvue-echarts'

  let chart = null
  export default {
    data () {
      return {
        option: {},
        echarts,
        person: {},
        dev: [],
        showDev: false,
        item: {},
        devSize: 0,
        switch1: false,
        selected: {},
        bind: false,
        tab_bol: 0,
        devNum: 0,
        onlineS: 0, // 当日累计在线时长
        percent_onlineS: 0,
        QQ_WECAT: 0, // QQ、微信在线时长
        percent_QQ_WECAT: 0,
        game: 0, // 游戏在线时长
        percent_game: 0,
        video: 0, // 视频时长
        percent_study: 0,
        study: 0, // 学习时长
        percent_video: 0,
        // 0 空白 1 有关注 2 无关注
        show: 0,
        fiveHourCount: 0,
        tjStartTime: new Date(new Date().setHours(0, 0, 0, 0)) / 1000,
        tjEndTime: new Date(new Date().setHours(23, 59, 59, 0)) / 1000,
        tjStartTimeT: new Date(new Date().setHours(0, 0, 0, 0)) / 1000,
        tjEndTimeT: new Date(new Date().setHours(23, 59, 59, 0)) / 1000,
        tjStartTimeY: new Date(new Date().setHours(0, 0, 0, 0) - 1000 * 60 * 60 * 24) / 1000,
        tjEndTimeY: new Date(new Date().setHours(23, 59, 59, 0) - 1000 * 60 * 60 * 24) / 1000,
        activeTime: this.$api.formatDate('MM月dd日', new Date()),
        xData: ['00:00', '01:00', '02:00', '03:00', '04:00', '05:00', '06:00', '07:00', '08:00', '09:00', '10:00', '11:00', '12:00', '13:00', '14:00', '15:00', '16:00', '17:00', '18:00', '19:00', '20:00', '21:00', '22:00', '23:00'],
        plan: [],
        checkedSwitch: false,
        state: false
      }
    },
    components: {
      mpvueEcharts
    },
    onLoad () {
      const firstCanvas = wx.createCanvasContext('firstCanvas')
      firstCanvas.setFillStyle('rgba(247,247,247,1)')
      firstCanvas.rect(0, 0, 93, 181)
      firstCanvas.fill()
      firstCanvas.draw()
      firstCanvas.setStrokeStyle('rgba(227,227,227,1)')
      for (var i = 0; i < 300; i += 5) {
        firstCanvas.beginPath()
        firstCanvas.moveTo(i, 0)
        firstCanvas.lineTo(0, i)
        firstCanvas.closePath()
        firstCanvas.stroke()
      }
      firstCanvas.draw()
      // firstCanvas.beginPath()
      // firstCanvas.moveTo(50, 0)
      // firstCanvas.lineTo(0, 100)
      // firstCanvas.closePath()
      // firstCanvas.stroke()
      // firstCanvas.draw()
      //
      // firstCanvas.beginPath()
      // firstCanvas.moveTo(90, 0)
      // firstCanvas.lineTo(0, 150)
      // firstCanvas.closePath()
      // firstCanvas.stroke()
      // firstCanvas.draw()

      const secondCanvas = wx.createCanvasContext('secondCanvas')
      secondCanvas.setFillStyle('rgba(255,255,255,1)')
      secondCanvas.rect(0, 0, 93, 181)
      secondCanvas.fill()
      secondCanvas.draw()

      const thirdCanvas = wx.createCanvasContext('thirdCanvas')
      thirdCanvas.setFillStyle('rgba(255,255,255,1)')
      thirdCanvas.rect(0, 0, 93, 181)
      thirdCanvas.fill()
      thirdCanvas.draw()

      const fourthCanvas = wx.createCanvasContext('fourthCanvas')
      fourthCanvas.setFillStyle('rgba(247,247,247,1)')
      fourthCanvas.rect(0, 0, 93, 181)
      fourthCanvas.fill()
      fourthCanvas.draw()
      fourthCanvas.setStrokeStyle('rgba(227,227,227,1)')
      for (var j = 0; j < 300; j += 5) {
        fourthCanvas.beginPath()
        fourthCanvas.moveTo(j, 0)
        fourthCanvas.lineTo(0, j)
        fourthCanvas.closePath()
        fourthCanvas.stroke()
      }
      fourthCanvas.draw()
    },
    onShow () {
      wx.showNavigationBarLoading()
      setTimeout(_ => {
        wx.hideNavigationBarLoading()
      }, 1000)
      // let carePerson = wx.getStorageSync('presonSelect')
      this.getCare()

    },
    onReady () {
      wx.removeStorageSync('selectedDev')
    },
    methods: {
      getDev () {
        var _this = this
        let uid = this.person['uid']
        if (uid) {
          this.$api.get('/dev', {'pid': uid, 'order': 'did'}, null, r => {
            _this.dev = r.data
            if (r.data.length > 0) {
              _this.devSize = r.total
              if (wx.getStorageSync('timeparams')) {
                _this.selected = wx.getStorageSync('selectedDev')
                wx.removeStorageSync('timeparams')
              } else {
                _this.selected = r.data[0]
                _this.devNum = 0
                wx.setStorageSync('selectedDev', r.data[0])
              }
              _this.checkedSwitch = _this.selected.block == 1 ? true : false
              _this.showDev = true
              _this.tjOnlineTime()
              _this.fiveHourData()
              _this.devplan()
            } else {
              _this.selected = {}
              _this.showDev = false
            }
          })

        }
      },
      findDev () {
        wx.redirectTo({
          url: '/pages/box/searchBox'
        })
      },
      switchChange (e) {
        if (this.state) {
          return
        }
        let dmac = this.selected.dmac
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
              _this.selected.block = obj.block
              wx.setStorageSync('selectedDev', _this.selected)
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
          },
          fail: err => {
            wx.showToast({
              title: res.data.message,
              icon: 'none',
              duration: 3000
            })
          }
        })

        wx.setStorageSync('block', obj)
      },
      changeSwitch () {
        this.switch1 = !this.switch1
        var bol = this.switch1
        // if (this.switch1) {
        //   bol = this.switch1
        // } else {
        //   bol = !this.switch1
        // }
        this.$api.put('/box/study/' + bol, null, null, r => {
        })
      },
      getData (index) {
        var _this = this
        this.devNum = index
        this.selected = this.dev[index]
        _this.checkedSwitch = this.selected.block == 1 ? true : false
        wx.setStorageSync('selectedDev', this.selected)

        this.tjOnlineTime()
        this.fiveHourData()
        this.devplan()
      },
      tjOnlineTime () {
        let params = {'dmac': this.selected.dmac, 'starttime': this.tjStartTime, 'endtime': this.tjEndTime}
        let onlineS = 0, QQ_WECAT = 0, game = 0, video = 0, study = 0
        this.$api.get('/activity/minnew', params, null, r => {
          r.data.forEach(function (v, k) {
            if (v.pkt >= 150) {
              onlineS++
              // sns: 0 QQ 1 微信 2 微博
              // atype: 1 game 2 video 3 study
              if ((v.sns >> 0) & 1 == 1 || (v.sns >> 1) & 1 == 1) {
                QQ_WECAT++
              }
              if (v.atype === 1) {
                game++
              }
              if (v.atype === 2) {
                video++
              }
              if (v.atype === 3) {
                study++
              }
            }
          })
          if (QQ_WECAT > onlineS) {
            QQ_WECAT = onlineS
          }
          let xx = onlineS > 180 ? onlineS : 180
          if (onlineS > 60) {
            this.onlineS = parseInt(onlineS / 60) + '小时'
            if (onlineS % 60 - (onlineS % 60) % 10 > 0) {
              this.onlineS += (onlineS % 60 - (onlineS % 60) % 10) + '分钟'
            }
          } else {
            if (onlineS - onlineS % 10 === 0) {
              this.onlineS = '不足10分钟'
              onlineS = 10
            } else {
              this.onlineS = onlineS - onlineS % 10 + '分钟'
            }
          }
          this.percent_onlineS = onlineS / xx * 100

          if (QQ_WECAT > 60) {
            this.QQ_WECAT = parseInt(QQ_WECAT / 60) + '小时'
            if (QQ_WECAT % 60 - (QQ_WECAT % 60) % 10 > 0) {
              this.QQ_WECAT += (QQ_WECAT % 60 - (QQ_WECAT % 60) % 10) + '分钟'
            }
          } else {
            if (QQ_WECAT - QQ_WECAT % 10 === 0) {
              this.QQ_WECAT = '不足10分钟'
              QQ_WECAT = 10
            } else {
              this.QQ_WECAT = QQ_WECAT - QQ_WECAT % 10 + '分钟'
            }
          }
          this.percent_QQ_WECAT = QQ_WECAT / xx * 100

          if (game > 60) {
            this.game = parseInt(game / 60) + '小时'
            if (game % 60 - (game % 60) % 10 > 0) {
              this.game += (game % 60 - (game % 60) % 10) + '分钟'
            }
          } else {
            if (game - game % 10 === 0) {
              this.game = '不足10分钟'
              game = 10
            } else {
              this.game = game - game % 10 + '分钟'
            }
          }
          this.percent_game = game / xx * 100

          if (video > 60) {
            this.video = parseInt(video / 60) + '小时'
            if (video % 60 - (video % 60) % 10 > 0) {
              this.video += (video % 60 - (video % 60) % 10) + '分钟'
            }
          } else {
            if (video - video % 10 === 0) {
              this.video = '不足10分钟'
              video = 10
            } else {
              this.video = video - video % 10 + '分钟'
            }
          }
          this.percent_video = video / xx * 100

          if (study > 60) {
            this.study = parseInt(study / 60) + '小时'
            if (study % 60 - (study % 60) % 10 > 0) {
              this.study += (study % 60 - (study % 60) % 10) + '分钟'
            }
          } else {
            if (study - study % 10 === 0) {
              this.study = '不足10分钟'
              study = 10
            } else {
              this.study = study - study % 10 + '分钟'
            }
          }
          this.percent_study = study / xx * 100
        })
      },
      changeCare () {

      },

      getCare () {
        let sessionId = wx.getStorageSync('sessionId')
        let headers = Object.assign({}, {'content-type': 'application/json;charset=utf-8', 'Cookie': sessionId})
        var _this = this
        wx.request({
          url: `https://www.hobox.com.cn:8082/api/test/app/person-cared`,
          method: 'GET',
          header: headers,
          success: res => {
            if (res.data.statusCode === 200 && res.data.data !== '' && res.data.data !== null) {
              res.data.data['imageurl'] = this.$api.ImgName(res.data.data['uicon'])
              _this.person = res.data.data
              _this.getDev()
              _this.show = 1
            } else if (res.data.statusCode == 400) {
              _this.show = 2
            } else {
              _this.show = 2
            }
          },
          fail: err => {
            wx.showToast({
              title: '加载失败',
              icon: 'none',
              duration: 3000
            })
          }
        })
      },
      find () {
        wx.navigateTo({
          url: '/pages/box/personselect'
        })
      },
      guide () {
        wx.navigateTo({
          url: '/pages/box/guide'
        })
      },
      carePerson () {
        wx.navigateTo({
          url: '/pages/box/personselect?pid=' + this.person.uid
        })
      },
      onChange (num) {
        this.tab_bol = num
        if (this.dev.length > 0) {
          if (num == 0) {
            this.tjStartTime = this.tjStartTimeT
            this.tjEndTime = this.tjEndTimeT
            this.tjOnlineTime()
            this.fiveHourData()
          } else {
            this.tjStartTime = this.tjStartTimeY
            this.tjEndTime = this.tjEndTimeY
            this.tjOnlineTime()
            this.fiveHourData()
          }
        } else {
          return
        }
      },
      initChart (date, pkt_other) {

        this.option = {
          grid: {
            top: '10rpx',
            bottom: '1rpx',
            left: '1rpx',
            right: '1rpx'
          },
          xAxis: {
            data: date,
            axisLabel: {
              show:false,
              interval: function (i, v) {
                return v == '00:00' || v == '04:00' || v == '08:00' || v == '12:00' || v == '16:00' || v == '20:00' || v == '23:00' ? v : ''
              }
            },
            axisLine: {
              lineStyle: {
                color: '#ddd' //坐标轴线颜色
              }
            },
            axisTick: {show: false}
          },
          yAxis: {
            min: 0,
            max: 2000,
            interval: 700,
            type: 'value',
            axisLabel: {
              formatter: function () {
                return ''
              }
            },
            splitLine: {
              show:false,
              lineStyle: {
                color: ['#eee']
              }
            },
            axisLine: {show: false},
            axisTick: {show: false}
          },
          dataZoom: [
            {
              type: 'slider',
              start: 0,
              end: 100,
              show: false,
              xAxisIndex: 0,
              filterMode: 'empty',
              handleIcon: 'M10.7,11.9v-1.3H9.3v1.3c-4.9,0.3-8.8,4.4-8.8,9.4c0,5,3.9,9.1,8.8,9.4v1.3h1.3v-1.3c4.9-0.3,8.8-4.4,8.8-9.4C19.5,16.3,15.6,12.2,10.7,11.9z M13.3,24.4H6.7V23h6.6V24.4z M13.3,19.6H6.7v-1.4h6.6V19.6z',
              handleStyle: {
                color: '#fff',
                shadowBlur: 3,
                shadowColor: 'rgba(0, 0, 0, 0.6)',
                shadowOffsetX: 2,
                shadowOffsetY: 2
              }
            },
            {
              type: 'inside',
              xAxisIndex: 0,
              filterMode: 'empty'
            }
          ],
          series: [
            {
              id: 'c',
              type: 'bar',
              data: pkt_other,
              barWidth:'1rpx',
            }
          ]
        }
        this.$refs.echarts.init()
      },
      handleInit (canvas, width, height) {
        chart = echarts.init(canvas, null, {
          width: width,
          height: height
        })
        canvas.setChart(chart)
        chart.setOption(this.option)
        return chart
      },
      fiveHourData () {
        let starttime = this.tjStartTime
        let endtime = this.tjEndTime
        wx.setStorageSync('starttime', starttime)
        let _this = this
        var timeinterval = 5
        this.$api.get('/activity/minnew', {
          'dmac': this.selected.dmac,
          'starttime': starttime,
          'endtime': endtime,
          'timeinterval': timeinterval
        }, null, r => {


          var list = r.data
          var pkt_other = [], date = [], obj = {}, listSize = list.length, listIndex = 0
          for (let i = 0, len = 60 * 24 / timeinterval; i < len; i++) {
            if (listIndex < listSize) {
              obj = list[listIndex]
            }

            if (starttime === obj['time']) {
              obj.pkt > 2000 ? obj.pkt = 2000 : obj.pkt
              if (obj.atype == 1) {
                pkt_other.push({
                  value: obj['pkt'],
                  itemStyle: {color: '#ed3f14'},
                  newData: {adesc: obj['adesc'], url: obj['url']}
                })
              } else if (obj.atype == 2) {
                pkt_other.push({
                  value: obj['pkt'],
                  itemStyle: {color: '#ff9920'},
                  newData: {adesc: obj['adesc'], url: obj['url']}
                })
              } else if (obj.atype == 3) {
                pkt_other.push({
                  value: obj['pkt'],
                  itemStyle: {color: '#800080'},
                  newData: {adesc: obj['adesc'], url: obj['url']}
                })
              } else {
                pkt_other.push({
                  value: obj['pkt'],
                  itemStyle: {color: '#15bbbc'},
                  newData: {adesc: obj['adesc'], url: obj['url']}
                })
              }
              date.push(_this.$api.formatDate('hh:mm', new Date(starttime * 1000)))
              listIndex++
            } else {
              pkt_other.push({value: 0, itemStyle: {color: '#15bbbc'}, newData: {adesc: obj[''], url: obj['']}})
              date.push(_this.$api.formatDate('hh:mm', new Date(starttime * 1000)))
            }
            starttime = starttime + timeinterval * 60

          }
          _this.initChart(date, pkt_other)
        })
      },
      chartDetail () {
        var params = {'dmac': this.selected.dmac, 'starttime':  this.tjStartTime, 'endtime': this.tjEndTime, today: this.tab_bol}
        wx.setStorageSync('timeparams', params)
        wx.navigateTo({
          url: '/pages/box/timeline'
        })

      },
      adddev () {
        wx.navigateTo({
          url: '/pages/box/devlist?devlist=2'
        })
      },
      addplan () {
        wx.navigateTo({
          url: '/pages/box/planadd?dmac=' + this.selected.dmac
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
          this.selected.block = 2
          wx.showToast({
            title: '成功',
            icon: 'none',
            duration: 1500
          })
          this.devplan()
        })
      },
      devplan () {
        this.$api.get('/plan/' + this.selected.dmac, null, null, r => {
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
      }
    }
  }
</script>

<style scoped>
  .page {
    background: #ffffff;
    height: auto;
  }

  .page__bd {
    background: #00bcd4;
    padding-bottom: 20rpx
  }

  .weui-grid__label {
    color: #ffffff;
  }

  .dev_online {
    background: #f2fbfc;
    color: #00bcd4;
  }

  .dev_offline {
    background: #eeeeee;
    color: #cccccc;
  }

  .iconcolor {
    font-size: 60rpx;
    line-height: 96rpx
  }

  .position {
    position: absolute;
    z-index: 999;
    width: 12px;
    height: 12px;
    right: 30rpx;
    top: 30rpx;
    text-align: center;
  }

  .weui-grids {
    border-top: 0px;
    border-left: 0px;
    /*border-bottom: 1px solid #d9d9d9;*/
  }

  .weui-grid {
    padding: 10px 10px 5px 10px;
    border-bottom: 0px;
    border-right: 0px;
  }

  .gzdiv {
    height: 50px;
    width: 80px;
    margin: 0px auto;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .gz {
    margin-top: 0px;
    height: 50rpx;
    width: 130rpx;
    border-radius: 40rpx;
    background: #ffffff;
    line-height: 50rpx;
    color: #00bcd4;
  }

  .bordercolor {
    border: 2rpx solid #00bcd4 !important;
  }

  .weui-media-box__title {
    height: 60rpx;
    text-align: center;
    line-height: 60rpx;
    border-bottom: 2px solid #eeeeee;
    position: relative;
  }

  .slide-box {
    display: -webkit-box;
    overflow-x: scroll;
    -webkit-overflow-scrolling: touch;
  }

  .slide-item {
    /*width: 50px;*/
    /*margin-right: 20px;*/
    padding: 20rpx;
    width: auto;
    text-align: center;
    width: 110rpx;
  }

  .pointDev {
    height: 96rpx;
    width: 96rpx;
    text-align: center;
    margin: 0 auto;
    border-radius: 50px;
    position: relative;
    border: 2rpx solid transparent;
  }

  .triangle_border_on {
    border-color: #00bcd4 transparent transparent; /*灰 透明 透明 */
  }

  .triangle_border_off {
    border-color: transparent transparent transparent; /*灰 透明 透明 */
  }

  .triangle_border_down {
    width: 0;
    height: 0;
    border-width: 10rpx 10rpx 0;
    border-style: solid;
    margin: 8rpx auto;
    position: relative;
  }

  .wrap {
    width: 80%;
    height: 100%;
    margin: 0 auto;
    z-index: -1;
  }

  .addcare {
    width: 470rpx;
    height: 84rpx;
    line-height: 84rpx;
    text-align: center;
    color: #ff9920;
    font-size: 32rpx;
    border: 1px solid #ff9920;
    border-radius: 10rpx;
    margin: 80rpx auto;
  }

  .title {
    height: 60rpx;
    line-height: 60rpx;
    font-size: 22rpx;
    /*float: right;*/
    margin-right: 40rpx;
    position: absolute;
    right: 40rpx;
    bottom: -70rpx;
  }

  .title div {
    float: left;
  }

  .welcome, .guide, .buy, .share {
    width: 90%;
    margin: 0 auto;
    border: 1px solid transparent;
    margin-bottom: 20rpx;
    background: #ffffff;
    border-radius: 16rpx;
    box-shadow: 0px 0px 2px #ddd;
  }

  .welcome {
    height: 320rpx;
    text-align: center;

  }

  .welcome h4 {
    font-weight: 600;
    margin-top: 53rpx;
    color: #333333;
    font-size: 32rpx;
    font-family: NotoSansSyriacEastern;
  }

  .welcome p {
    color: #9A9A9A;
    font-size: 24rpx;
    margin-top: 20rpx;
    margin-bottom: 60rpx;
  }

  .welcome span {
    font-size: 28rpx;
    color: #00BCD4;
  }

  .welcome span i {
    display: inline-block;
    font-size: 18rpx;

  }

  .guide {
    height: 200rpx;
    position: relative;
  }

  .buy {
    height: 320rpx;
  }

  .share {
    height: 92rpx;
    line-height: 92rpx;
    background: #00BCD4;
    color: #ffffff;
    font-size: 34rpx;
  }

  .share i {
    display: inline;
    margin-left: 24rpx;
    margin-right: 24rpx;
  }

  .guide h3, .buy h3 {
    color: #333333;
    font-weight: 100;
    font-size: 32rpx;
    margin-top: 33rpx;
    margin-left: 40rpx;
    margin-bottom: 20rpx;
  }

  .guide p {
    color: #9A9A9A;
    font-size: 24rpx;
    margin-left: 40rpx;
  }

  .guide img {
    width: 124rpx;
    height: 124rpx;
    position: absolute;
    right: 40rpx;
    top: 38rpx;
  }

  .buy img {
    display: block;
    margin: 0 auto;
    width: 600rpx;
    height: 200rpx;
  }

  .weui-cells:before {
    top: 0;
    border-top: 0px;
  }

  .weui-cells {
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

  .weui-grid {
    border-bottom: 0px;
    border-right: 0px;
    width: 50%;
    padding: 0px 0px;
  }

  .my_detail {
    height: 60rpx;
    width: 60rpx;
  }

  .my_img {
    width: 100%;
    height: 100%;
  }

  .weui-media-box__title span {
    font-size: 20rpx;
    margin-left: 40rpx;
    color: #999;
  }

  .plan {
    height: 80rpx;
  }

  .plan .weui-grid {
    position: relative;
    height: 50rpx;
    margin-top: 15rpx;
  }

  .plan .weui-grid img {
    width: 40rpx;
    height: 40rpx;
    position: absolute;
    left: 30%;
    top: 50%;
    margin-top: -20rpx;
  }

  .plan .weui-grid__label {
    margin-left: 40rpx;
    position: absolute;
    left: 35%;
    top: -2%;
  }

  .person {
    height: 313rpx;
    background: #fff;
    position: relative;
  }

  .person img.img_bg {
    width: 100%;
    height: 200rpx;
  }

  .person img.img_person {
    width: 148rpx;
    height: 148rpx;
    border-radius: 50%;
    position: absolute;
    left: 28rpx;
    bottom: 45rpx;
    border: 3px solid #00BCD4;
  }

  .name {
    color: #333333;
    font-weight: 600;
    font-size: 36rpx;
    position: absolute;
    left: 210rpx;
    bottom: 50rpx;

  }

  .name span {
    float: left;
  }

  .name span.fouce_name {
    display: inline-block;
    height: 40rpx;
    text-align: center;
    line-height: 40rpx;
    margin-left: 20rpx;
    max-width: 200rpx;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap
  }

  .name span.change_focus {
    display: inline-block;
    width: 133rpx;
    height: 40rpx;
    text-align: center;
    line-height: 40rpx;
    font-size: 24rpx;
    color: #00BCD4;
    border: 1px solid #00BCD4;
    border-radius: 30rpx;
    margin-left: 20rpx;
    max-width: 300rpx;

  }

  .dev {
    height: 190rpx;
    border-top: 30rpx solid #f5f5f5;
    background: #fff;
    box-shadow: 0 5rpx 10rpx #ddd;

  }

  .main {
    margin-top: 10rpx;
    min-height: 600rpx;
    background: #ffffff;
  }

  .switch_group {
    position: absolute;
    right: 26rpx;
    top: 130rpx;
  }

  .weui-grids {
    height: 190rpx;

  }

  .weui-grid {
    height: 190rpx;
    width: 150rpx;
    margin-left: 20rpx;
    position: relative;
  }

  .weui-grid .weui-grid__icon {
    border-radius: 50%;
    width: 96rpx;
    height: 96rpx;
  }

  .active::after {
    content: '';
    display: inline-block;
    width: 0px;
    height: 0;
    border-bottom: 25rpx transparent solid;
    border-left: 25rpx transparent solid;
    border-right: 25rpx transparent solid;
    border-top: 10rpx #00BCD4 solid;
    position: absolute;
    top: 0;
    left: 50%;
    margin-left: -25rpx;
  }

  .active .pointDev {
    border: 2rpx solid #00bcd4 !important;
  }

  .weui-grid__label {
    color: #999;
  }

  ul {
    width: 100%;
    height: 70rpx;
    background: #fff;
  }

  ul, li {
    list-style: none;
  }

  li {
    display: inline-block;
    width: 110rpx;
    height: 70rpx;
    float: left;
    line-height: 70rpx;
    text-align: center;
    margin-left: 30rpx;
    color: #333333;
    font-size: 24rpx;
    border-bottom: 1px solid transparent;
  }

  .tab_active {
    color: #00BCD4;
    border-bottom: 1px solid #00BCD4;
  }

  .echarts_group1 {
    min-height: 400rpx;
    height: auto;
    position: relative;
  }

  .echarts_group2 {
    min-height: 400rpx;
    height: auto;
  }

  .dev .weui-grid__icon {
    margin-top: 30rpx;
    margin-bottom: 15rpx;
  }

  .echart_detail {
    width: 90%;
    margin: 0 auto;
  }

  .noDev {
    height: 190rpx;
    line-height: 190rpx;
    background: #ffffff;
  }

  .weui-cells {
    margin-bottom: 0;
  }

  .mini-btn {
    display: inherit;
    height: 60rpx !important;
    line-height: 60rpx !important;
    width: 200rpx !important;
    border: 2rpx solid #00bcd4;
    color: #00bcd4;
    font-size: 28rpx;

  }

  .i-swipeout-demo-button {
    width: 140rpx;
    float: left;
    display: flex;
    height: 100%;
    justify-content: center;
    background: #2d8cf0;
    color: #fff;
    align-items: center;

  }

  .i-swipeout-demo-button-group {
    width: 100%;
    height: 100%
  }

  .planDiv {
    min-height: 212rpx;
    margin-bottom: 20rpx;
    background: #fff;
  }

  #studyProgress .i-progress-bg {
    background: red;
  }

  .ec-canvas {
    position: absolute;
    left: 0;
    top: 0;
    z-index: 0 !important;
  }
  .canvas_title_div{
    position:absolute;
    left:5%;
    top:430rpx;
    width:90%;
    height:30rpx;

  }
  .canvas_title{
   display: inline-block;
    width: 25%;
    text-align: left;
    color: #aaa;
    font-size: 12px;
  }

</style>
