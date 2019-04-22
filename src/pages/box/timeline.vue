<template>
  <div id="page" class="page" style="overflow:hidden;white-space:nowrap">
    //横向滑动
    <scroll-view class="scroll-view_H" scroll-x :scroll-into-view="toHour"  scroll-with-animation>
      <ul class="hoursList">
        <li v-for="(item,index) in hours" :key="item" @click="getOneHourLog(index,item)" :id="'hour_'+index">
        <span :class="index===hourActive?'active':''">
        {{item.num}}
        </span>
          <ul class="pointsList" v-if="hoursList[index]">
            <li v-if="hoursList[index].points.study" :class="hoursList[index].points.study?'study':''"></li>
            <li v-if="hoursList[index].points.video" :class="hoursList[index].points.video?'video':''"></li>
            <li v-if="hoursList[index].points.QQ_WECAT" :class="hoursList[index].points.QQ_WECAT?'QQ_WECAT':''"></li>
            <li v-if="hoursList[index].points.game" :class="hoursList[index].points.game?'game':''"></li>
          </ul>
        </li>
      </ul>
    </scroll-view>
    //纵向滑动
    <scroll-view scroll-y   :scroll-into-view="toLi" scroll-with-animation
                 :style="{height: (screenH-70)+'px;'}" class="timeDiv">
      <!--<div v-for='(item ,index) in internetLogList' :key="index"  :id="'div_'+ index" class="timeLine_div" ref="ass">-->
      <div class="timeUl" ref="timeUl">
        <ul class="hour_line_detail">
          <!--<li class="clock"><b>9:00</b> <img src="" alt=""><span>微信</span></li>-->
          <li v-for="(sub_item ,sub_index) in internetLogList" :key="sub_item"
              :class="[sub_item.value===0?'noVaule':'hasVaule']" :id="'li_'+sub_index">
            <b v-if="sub_index===0">{{hour+':00'}}</b>
            <b v-else-if="sub_index===5">{{hour+':05'}}</b>
            <b v-else-if="sub_index%5===0">{{hour+':'+sub_index}}</b>
            <b v-else></b>
            <img v-if="sub_item.newData.url" :src=sub_item.newData.url alt=""> <span>{{sub_item.newData.adesc}}</span>
          </li>
        </ul>
      </div>
    </scroll-view>
    //搜索按钮
    <div class="search" style="" @click='searchBtn=!searchBtn' v-if="searchBtn">
      <i></i>
      <p style="">筛选</p>
    </div>
    //搜索操作
    <div :style="{height: screenH+'px',width:screenW+'px'}" class="fullScreen" v-show="!searchBtn">
      <div class="searchArea" :style="{height: screenH+'px;'}">
        <h4>筛选</h4>
        <p v-for="(item,index) in searchOpeart" :key="index" :class="searchOpeartActive===index?'active':''"
           @click="searchState(index)">
          {{item}}
        </p>
      </div>
    </div>
    <i-spin size="large" fix v-if="spinShow"></i-spin>
  </div>

</template>
<script>
  export default {
    data () {
      return {
        hours: [],
        hoursList: [],
        hourActive: '',
        hour: '0000',
        toHour: 'hour_' + this.hour,
        toLi: 'li_0',
        internetLogList: [],
        allData: [],
        studyData: [],
        wechatData: [],
        gameData: [],
        videoData: [],
        searchOpeartActive: '10',
        screenW: '',
        screenH: '',
        timeData: [],
        searchOpeart: ['显示游戏', '显示视频', '显示学习', '显示其他', '显示所有', '显示有数据'],
        searchBtn: true,
        searchData: '',
        dev: '',
        starttime: '',
        endtime: '',
        spinShow: true
      }
    },

    onShow () {
      this.sethours()
      this.internetLogList = []
      this.hoursList = []
      this.hour = -1
      this.searchBtn = true
      this.dev = wx.getStorageSync('timeparams')
      console.log(this.dev.dmac)
      this.starttime = wx.getStorageSync('timeparams').starttime
      this.endtime = wx.getStorageSync('timeparams').endtime
      this.getHoursState(0, 23)
      if (this.dev.today == 0) {
        this.hourActive = new Date().getHours()
        this.toHour = 'hour_' + (this.hourActive + 1)
        this.getOneHourLog(this.hourActive, this.hours[this.hourActive])
      } else {
        this.hourActive = 0
        this.toHour = 'hour_0'
        this.getOneHourLog(this.hourActive, this.hours[this.hourActive])
      }
      this.screenW = wx.getSystemInfoSync().windowWidth
      this.screenH = wx.getSystemInfoSync().windowHeight
      this.spinShow = true
    },
    methods: {
      sethours () {
        this.hours=[]
        for (var i = 0; i < 24; i++) {
          this.hours.push({num: i})
        }
      },
      setActive (index) {
        this.hourActive = index
      },
      //获取一个小时数据
      getOneHourLog (index, item) {
        if (this.hour == index) {
        } else {
          this.hour = item.num
          this.setActive(index)
          // this.getOneHourLog(item.num)
          this.toHour = 'hour_' + (this.hour - 3)
          this.toLi = 'li_0'
          var starttime = this.starttime + item.num * 60 * 60
          var endtime = starttime + 60 * 60
          var _this = this
          var params = {'dmac': this.dev.dmac, 'starttime': starttime, 'endtime': endtime}
          this.$api.get('/activity/minnew', {
            'dmac': params.dmac,
            'starttime': params.starttime,
            'endtime': params.endtime
          }, null, r => {
            var starttime = params.starttime
            var list = r.data
            var pkt_other = []
            var date = []
            var obj = {}
            var listSize = list.length
            var listIndex = 0
            for (let i = 0, len = 60; i < len; i++) {
              if (listIndex < listSize) {
                obj = list[listIndex]
              }
              if (starttime == obj['time']) {
                pkt_other.push({
                  value: obj['pkt'],
                  newData: {adesc: obj['adesc'], url: obj['url']}
                })
                listIndex++
              } else {
                pkt_other.push({value: 0, newData: {adesc: obj[''], url: obj['']}})
              }
              starttime = starttime + 60
            }
            _this.internetLogList = pkt_other
          })
        }
      },
      //判断24小时 视频、游戏、学习、微信等使用情况
      getHoursState (i, len) {
        var _this = this
        var starttime = this.starttime + i * 60 * 60
        var endtime = starttime + 60 * 60
        let params = {'dmac': this.dev.dmac, 'starttime': starttime, 'endtime': endtime}
        let points = {
          game: false,
          video: false,
          study: false,
          QQ_WECAT: false
        }
        this.$api.get('/activity/minnew', params, null, r => {
          r.data.forEach(function (v, k) {
            // sns: 0 QQ 1 微信 2 微博
            // atype: 1 game 2 video 3 study
            if ((v.sns >> 0) & 1 === 1 || (v.sns >> 1) & 1 === 1) {
              points.QQ_WECAT = true
            }
            if(v.atype === 0){
              points.QQ_WECAT = true
            }
            if (v.atype === 1) {
              points.game = true
            }
            if (v.atype === 2) {
              points.video = true
            }
            if (v.atype === 3) {
              points.study = true
            }
          })
          var item = {
            num: i,
            points: points
          }
          _this.hoursList.push(item)
          _this.allData = _this.hoursList
          if (i++ < len) {
            _this.getHoursState(i, len)
          }
          if (i > 23) {
            setTimeout(function () {
              _this.spinShow = false
            }, 3000)
          }
        })
      },
      searchState (index) {
        var _this = this
        _this.searchOpeartActive = index
        if (index === 0) {
          this.getGameState()
        } else if (index === 1) {
          this.getVideoState()
        } else if (index === 2) {
          this.getStudyState()
        } else if (index === 3) {
          this.getWechatState()
        } else if (index === 4) {
          this.getAllState()
        } else if (index === 5) {
          this.getHasState()
        }

        if (this.searchData.length > 0) {
          this.searchBtn = true
          if (this.searchData.length === 24) {
            _this.hoursList = this.searchData
            _this.hours = this.searchData
            setTimeout(function () {
              _this.toHour = 'hour_' + (new Date().getHours() - 3)
            }, 100)
          } else {
            setTimeout(function () {
              _this.toHour = 'hour_0'
            }, 100)
            _this.hoursList = this.searchData
            _this.hours = this.searchData
            _this.hourActive = 0
            _this.hour = _this.hoursList[0].num
            _this.getOneHourLog(_this.hoursList[0].num)
          }
        } else {
          wx.showToast({
            title: '无数据',
            icon: 'none',
            duration: 800
          })
        }
      },
      getStudyState () {
        var _this = this
        _this.studyData = []
        for (var i = 0; i < 24; i++) {
          if (_this.allData[i].points.study === true) {
            _this.studyData.push(_this.allData[i])
          }
        }
        this.searchData = _this.studyData
      },
      getGameState () {
        var _this = this
        _this.gameData = []
        for (var i = 0; i < 24; i++) {
          if (_this.allData[i].points.game === true) {
            _this.gameData.push(_this.allData[i])
          }
        }
        this.searchData = _this.gameData
      },
      getVideoState () {
        var _this = this
        _this.videoData = []
        for (var i = 0; i < 24; i++) {
          if (_this.allData[i].points.video === true) {
            _this.hour = _this.hoursList[0].num
            _this.videoData.push(_this.allData[i])
          }
        }
        this.searchData = _this.videoData
      },
      getWechatState () {
        var _this = this
        _this.wechatData = []
        for (var i = 0; i < 24; i++) {
          if (_this.allData[i].points.QQ_WECAT === true) {
            _this.wechatData.push(_this.allData[i])
          } else {
            _this.hour = 0
          }
        }
        this.searchData = _this.wechatData
      },
      getAllState () {
        this.searchData = this.allData
        this.hourActive = new Date().getHours()
        this.hour = new Date().getHours()

        this.getOneHourLog(this.hour)
      },
      getHasState () {
        var _this = this
        _this.timeData = []
        for (var i = 0; i < 24; i++) {
          if (_this.allData[i].points.QQ_WECAT === true || _this.allData[i].points.game || _this.allData[i].points.study || _this.allData[i].points.video) {
            _this.timeData.push(_this.allData[i])
          } else {
            _this.hour = 0
          }
        }
        this.searchData = _this.timeData
      }
    }

  }
</script>
<style scoped>
  .fullScreen{
    position: absolute;
    left: 0;
    top: 0;
    background: rgba(0,0,0,0.5);
  }
  .searchArea{
    position: absolute;
    right: 0;
    top:0rpx;
    width: 390rpx;
    background: #fff;
    text-align: center;
  }
  .search i{
display: inline-block;
    width: 38rpx;
    height: 38rpx;
    background: url("../../../static/icon/search.png");
    background-size: 100% 100%;
  }
  .searchArea h4{
    text-align: left;
    width: 100%;
    height: 95rpx;
    line-height: 95rpx;
    color: #333333;
    font-weight: 400;
    border-bottom: 1px solid #F7F7F7;
    font-size: 25rpx;
    text-indent: 32rpx;
    margin-bottom: 40rpx;
  }
  .searchArea p{
    width: 320rpx;
    height: 80rpx;
    margin: 0 auto;
    margin-bottom: 25rpx;
    text-align: center;
    line-height: 80rpx;
    color: #999;
    background: #EEEEEE;
    font-size: 25rpx;

  }
  .searchArea p.active{
    background: #BFF2F9;
    color: #01CDE8;

  }
  .study{
    background: #19be6b;
  }
  .game{
    background: #ed3f14;
  }
  .video{
    background: #800080;
  }
  .QQ_WECAT{
    background: #01CDE8;
  }
  .pointsList{
    text-align: center;
    position: absolute;
    left: 0;
    bottom: -4rpx;
    width: 100rpx;
  }
  .pointsList li{
    display: inline-block;
    width: 10rpx;
    height: 10rpx;
    border-radius: 50%;
    margin: 5rpx;
  }
  .search {
    position: fixed;
    right: 0;
    top: 0;
    width: 88rpx;
    padding: 10rpx 0;
    height: 80rpx;
    z-index: 100;
    box-shadow: -5rpx -2rpx 8rpx #ddd;
    background: #fff;
    text-align: center;
  }
  #hour_23 {
    width: 150rpx;
    height: 100rpx;
    position: relative;
  }
  #hour_23 span{
    position: absolute;
    left: 28rpx;
    top: 20rpx;
  }
  .search p {
    color: #999;
    font-size: 18rpx;
    width: 100%;
    text-align: center
  }
  .search i{
    color: #01CDE8;
  }
  .scroll-view_H {
    overflow: hidden;
    background: #fff;
    white-space: nowrap;
    width: 100%;
    height: 100rpx;
    position: fixed;
    top: 0;
    left: 0;
    padding-right: 100rpx;
  }

  .scroll-view_H .hoursList>li {
    display: inline-block;
    width: 100rpx;
    height: 100rpx;
    position: relative;
  }
  .scroll-view_H .hoursList>li span{
    display: inline-block;
    width: 55rpx;
    height: 55rpx;
    border-radius: 50%;
    position: absolute;
    left: 50%;
    margin-left: -27.5rpx;
    top: 20rpx;
    text-align: center;
    line-height: 55rpx;
    color: #666666;
    font-size: 25rpx;
  }
  .scroll-view_H .hoursList>li span.active{
    background: #01CDE8;
    box-shadow: 0px 0px 5px #01CDE8;
    color: #ffffff;
  }
  .hour_line_detail{
    width: 100%;
    margin: 0 auto;
    border-left: 1px solid #C5C5C5;
  }
  .hour_line_detail li{
    display:block;
    width: 100%;
    height: 80rpx;
    line-height: 80rpx;
    margin: 0;
    position: relative;
  }
  .hour_line_detail li img{
    width: 50rpx;
    height: 50rpx;
    position: absolute;
    left: 170rpx;
    top: 15rpx;
  }
  .hour_line_detail li span{
    display: inline-block;
    height: 80rpx;
    line-height: 80rpx;
    position: absolute;
    left: 250rpx;
    top: 0;
    max-width: 360rpx;
    overflow:hidden;
    text-overflow:ellipsis;
    white-space:nowrap
  }
  .hour_line_detail li:before{
    display: inline-block;
    height: 80rpx;
    line-height: 80rpx;
    width: 17rpx;
    content:"-";
    color:#C5C5C5;
    font-size:20px;
    position: absolute;
    left: 130rpx;
    top:0;
    border-right: 1px solid #C5C5C5;
  }
  .hour_line_detail li.clock:before{
    display: inline-block;
    height: 80rpx;
    line-height: 80rpx;
    width: 30rpx;
    content:".";
    color:#C5C5C5;
    font-size:40px;
    position: absolute;
    left: 150rpx;
    top: -30rpx;
  }
  .hour_line_detail li b{
    position: absolute;
    display: inline-block;
    height: 80rpx;
    line-height: 80rpx;
    width: 50rpx;
    left:40rpx;
    top: 50%;
    margin-top: -35rpx;
  }
  .timeDiv{
    position: fixed;
    left: 0;
    top: 105rpx;
  }
  ::-webkit-scrollbar {
    width: 0;
    height: 0;
    color: transparent;
  }
  .timeDiv::-webkit-scrollbar{
    width: 0;
    background: transparent;
  }
  .noVaule{
    background: #f3f3f3;
  }
</style>
