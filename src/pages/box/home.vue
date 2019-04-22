<template>
  <div id="page" class="page">
    <div v-show="bind" style="background: #1E112F;width: 100%;height: 100%">
      <div class="personArea">
        <div class="circleLine circle_big"></div>
        <div class="circleLine circle_middle"></div>
        <div class="circleLine circle_small1"></div>
        <div class="circleLine circle_small2"></div>
        <div class="circleLine circle_small3"></div>
        <ul>
          <li class="circle circle55">
            <i-avatar v-if='user.online'v-bind:src="user.imageurl" size="large" i-class="personavatar" @click="personinfo(100)"></i-avatar>
            <i-avatar v-else v-bind:src="user.imageurl" size="large" i-class="personavatar_off" iimage="imagegray" @click="personinfo(100)"></i-avatar>
            <span class="userName">{{user.nickname}}</span>
          </li>
          <li v-for="(p,index) in person" :class="p.classNum" :key="p" @click="personinfo(index)" v-if="index<13">
            <!--<div v-if="p.num=='10'||p.num=='01'||p.num=='21'||p.num=='12'||p.num=='03'||p.num=='23'">-->
            <div v-if="index%2!=0">
              <i-avatar v-if="p.online" v-bind:src="p.imageurl" size="default" i-class="personavatar"></i-avatar>
              <i-avatar v-else v-bind:src="p.imageurl" size="default" i-class="personavatar_off"
                        iimage="imagegray"></i-avatar>
            </div>
            <div v-else>
              <i-avatar v-if="p.online" v-bind:src="p.imageurl" size="mini" i-class="personavatar"></i-avatar>
              <i-avatar v-else v-bind:src="p.imageurl" size="mini" i-class="personavatar_off"
                        iimage="imagegray"></i-avatar>
            </div>
            <span class="userName">{{p.nickname}}</span>
          </li>
          <li v-if="person.length>12" class="circle circle04 c12" @click="goPerson"></li>
          <li v-if="person.length>12" class="circle circle05 c13" @click="goPerson"></li>
          <li v-if="person.length>12" class="circle circle14 c14" @click="goPerson"></li>
          <li v-if="person.length>12" class="circle circle15 c15" @click="goPerson"></li>
          <li v-if="person.length>12" class="circle circle24 c16" @click="goPerson"></li>
          <li v-if="person.length>12" class="circle circle25 c17" @click="goPerson"></li>
        </ul>
      </div>
      <div class="addPerson" @click="addPerson">
        <i class="iconfont icon-tianjiarenyuan" style="font-size:24px;color:#00bcd4;line-height: 72rpx;margin-left: 20rpx"></i>
      </div>
      <p class="tip" v-if="noticeShow">
        家小盒离线，请检查供电及网络情况
      </p>
      <p class="lastOff"  @click="presentmore">
        <span>最新{{present.onoff === 1?'上线':'下线'}}</span>
        <!--<span v-if="">{{present.onoff === 1?'上线':'下线'}}</span>-->
        <!--<span  v-else>下线 </span>-->
        <span v-if="present.dname!=''" style="max-width: 200rpx; overflow:hidden;text-overflow:ellipsis;white-space:nowrap"> {{ present.pname}}[{{ present.dname}}] </span>
        <span> {{ present.timeStr }}</span>
      </p>
    </div>
    <div v-show="!bind "   style="padding-top: 30rpx;background: -webkit-linear-gradient(rgba(1,205,232,0.7) 0%,  #fff 40%);background: -o-linear-gradient(rgba(1,205,232,0.7) 0%,  #fff 40%);     background: -moz-linear-gradient(rgba(1,205,232,0.7) 0%,  #fff 40%);background: linear-gradient(rgba(1,205,232,0.7) 0%, #fff 40%);">
      <div class="welcome">
        <h4>欢迎使用家小盒</h4>
        <p >绑定设备，让家小盒帮您无时无刻的管理网络</p>
        <span @click="findDev()"><i class="iconfont icon-add" style=""></i>添加设备</span>
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
  </div>

</template>
<script>
  import api from '@/api/index'

  export default {
    data () {
      return {
        arr0: [0, 1, 2, 3, 4, 5],
        arr1: [0, 1, 2, 3, 4, 5],
        arr2: [0, 1, 2, 3, 4, 5],
        count0: 0,
        count1: 0,
        count2: 0,
        person: [],
        uid: [],
        user: '',
        present: '',
        bind: false,
        noticeShow: false
      }
    },
    onLoad () {
      // var wxbind = wx.getStorageSync('wxbind')
      // this.bind = wxbind
      // if (wxbind) {
      //   this.getBoxState()
      //   this.presentData()
      //   this.getUser()
      // }

    },
    onShow () {
      var wxbind = wx.getStorageSync('wxbind')
      this.bind = wxbind
      if (wxbind) {
        this.getBoxState()
        this.presentData()
        this.persononline()
        this.getUser()
      }
    },
    methods: {
      goPerson () {
        wx.navigateTo({
          url: '/pages/box/personlist'
        })
      },
      findDev () {
        wx.navigateTo({
          url: '/pages/box/searchBox'
        })
      },
      guide () {
        wx.navigateTo({
          url: '/pages/box/guide'
        })
      },
      getBoxState () {
        let user = wx.getStorageSync('user')
        this.$api.get('/box/all', {'bmac': user['box'].bmac}, null, r => {
          r.data[0].bstatus === 0 ? this.noticeShow = true : this.noticeShow = false
        })
      },
      addPerson () {
        wx.navigateTo({
          url: '/pages/box/addperson'
        })
      },
      persononline () {
        this.person = []
        this.count0 = 0
        this.count1 = 0
        this.count2 = 0
        let _this = this
        this.$api.get('/person', {online: 1}, null, r1 => {
          let personOn = r1.data
          personOn.forEach(function (v1, k1) {
            v1['online'] = true
            v1['imageurl'] = api.ImgName(v1.uicon)
            _this.person.splice(_this.person.length, 0, v1)
          })
          this.$api.get('/person', {online: 2}, null, r2 => {
            let personOff = r2.data
            personOff.forEach(function (v2, k2) {
              v2['online'] = false
              v2['imageurl'] = api.ImgName(v2.uicon)
              _this.person.splice(_this.person.length, 0, v2)
            })
            for (var i = 0; i < _this.person.length; i++) {
              if (_this.person[i].uid == _this.user.uid) {
                var usr = _this.person.splice(i, 1)
                _this.user = usr[0]
              }
            }
            this.person.forEach(function (v, k) {
              if (k % 3 == 0) {
                v.classNum = 'circle circle0  circle0' + _this.arr0[_this.count0] + ' c' + k
                v.num = _this.arr0[_this.count0]
                _this.count0++
              } else if ((k - 1) % 3 == 0) {
                v.classNum = 'circle circle1  circle1' + _this.arr1[_this.count1] + ' c' + k
                v.num = _this.arr1[_this.count1]
                _this.count1++
              } else if ((k - 2) % 3 == 0) {
                v.classNum = 'circle circle2  circle2' + _this.arr2[_this.count2] + ' c' + k
                v.num = _this.arr2[_this.count2]
                _this.count2++
              }
            })
          })
        })
      },
      getUser () {
        var _this = this
        this.$api.get('/manager', null, null, r => {
          if (r.data !== 'null' && r.data !== '') {
            r.data['imageurl'] = '/static/icon/user_07.png'
            _this.user = r.data
          }
        })
      },
      personinfo (index) {
        var person = ''
        if (index == 100) {
          person = this.user
        } else {
          person = this.person[index]
        }
        wx.setStorageSync('personEdit', person)
        wx.navigateTo({
          url: '/pages/box/persondetail'
        })
      },
      presentmore () {
        wx.navigateTo({
          url: '/pages/box/presentlist'
        })
      },
      presentData () {
        api.get('/present', {'limit': 1}, null, r => {

          if (r.data.length > 0) {
            let _present = r.data[0]
            _present['timeStr'] = api.formatDate('hh:mm', new Date(_present.time * 1000))
            if (_present.pid === 0) {
              _present['imageurl'] = '/static/icon/user_00.png'
            } else {
              _present['imageurl'] = api.ImgName(_present.uicon)
            }
            if (!_present.dname) {
              api.get('/dev/' + _present.dmac, null, null, r => {
                let dev = r.data
                if (dev.hostname) {
                  _present.dname = dev.hostname
                } else if (dev.netbios) {
                  _present.dname = dev.netbios
                } else {
                  _present.dname = dev.oui
                }
              })
            }
            this.present = _present
          }
        })
      }
    }
  }
</script>
<style scoped>
  #page{
    width: 100%;
    height: 100%;

  }
  .addPerson{
    width: 75rpx;
    height: 72rpx;
    border-radius:50% 0 0 50% ;
    background: #fff;
    position: absolute;
    right: 0;
    top: 30rpx;
  }
  .lastOff{
    min-width: 417rpx;
    min-height: 67rpx;
    padding:5rpx 10rpx ;
    border-radius:50rpx ;
    position: absolute;
    border:1px solid #01CDE8;
    left: 50%;
    margin-left: -208rpx;
    bottom: 89rpx;
    color:#999;
    text-align: center;
    line-height: 67rpx;
    font-size: 20rpx;
text-indent: 20rpx;
  }
  .lastOff span{
    float: left;
    display: inline-block;
  }
  ul {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    margin: 0 auto;
    height: 600rpx;
    transform: scale(1.2);
  }
  ul li{
    position: absolute;
    left: 50%;
    top: 50%;
  }

  ul li.circle {
    text-align: center;
    border-radius: 50%;
    position: absolute;

  }
  ul li.circle55{
    width: 120rpx;
    height: 120rpx;
    position: absolute;
    left: 50%;
    top: 50%;
    margin-left: -60rpx;
    margin-top: -60rpx;
    border-radius: 50%;
  }
  ul li.circle00{
    width: 60rpx;
    height: 60rpx;
    position: absolute;
    left: 21%;
    top: 33%;
    margin-top: -30rpx;
    margin-left: -30rpx;
  }
  ul li.circle01{
    width: 100rpx;
    height: 100rpx;
    position: absolute;
    left: 27%;
    top: 50%;
    margin-top: -50rpx;
    margin-left: -50rpx;
  }
  ul li.circle02{
    width: 60rpx;
    height: 60rpx;
    position: absolute;
    left: 50%;
    top: 85%;
    margin-top: -30rpx;
    margin-left: -30rpx;
  }
  ul li.circle03{
    width: 100rpx;
    height: 100rpx;
    position: absolute;
    left: 40%;
    top: 30%;
    margin-left: -50rpx;
    margin-top: -50rpx;
  }
  ul li.circle04{
    width: 20rpx;
    height: 20rpx;
    position: absolute;
    left: 10%;
    top: 50%;
    margin-top: -10rpx;
    margin-left: -10rpx;
  }
  ul li.circle05{
    width: 20rpx;
    height: 20rpx;
    position: absolute;
    left: 32%;
    top: 15%;
    margin-top: -10rpx;
    margin-left: -10rpx;
  }
  ul li.circle10{
    width: 100rpx;
    height: 100rpx;
    position: absolute;
    left: 60%;
    top: 70%;
    margin-top: -50rpx;
    margin-left: -50rpx;
  }
  ul li.circle11{
    width: 60rpx;
    height: 60rpx;
    position: absolute;
    left: 50%;
    top: 15%;
    margin-top: -30rpx;
    margin-left: -30rpx;
  }
  ul li.circle12{
    width: 100rpx;
    height: 100rpx;
    position: absolute;
    left: 73%;
    top: 50%;
    margin-top: -50rpx;
    margin-left: -50rpx;
  }
  ul li.circle13{
    width: 60rpx;
    height: 60rpx;
    position: absolute;
    left: 78%;
    top: 68%;
    margin-top: -30rpx;
    margin-left: -30rpx;
  }
  ul li.circle14{
    width: 20rpx;
    height: 20rpx;
    position: absolute;
    left: 30%;
    top: 86%;
    margin-top: -10rpx;
    margin-left: -10rpx;
  }
  ul li.circle15{
    width: 20rpx;
    height: 20rpx;
    position: absolute;
    left: 69%;
    top: 85%;
    margin-top: -10rpx;
    margin-left: -10rpx;
  }
  ul li.circle20{
    width: 60rpx;
    height: 60rpx;
    position: absolute;
    left: 78%;
    top: 33%;
    margin-top: -30rpx;
    margin-left: -30rpx;
  }
  ul li.circle21{
    width: 100rpx;
    height: 100rpx;
    position: absolute;
    left: 60%;
    top: 30%;
    margin-top: -50rpx;
    margin-left: -50rpx;
  }
  ul li.circle22{
    width: 60rpx;
    height: 60rpx;
    position: absolute;
    left:21%;
    top: 68%;
    margin-top: -30rpx;
    margin-left: -30rpx;
  }
  ul li.circle23{
    width: 100rpx;
    height: 100rpx;
    position: absolute;
    left: 40%;
    top: 70%;
    margin-top: -50rpx;
    margin-left: -50rpx;
  }
  ul li.circle24{
    width: 20rpx;
    height: 20rpx;
    position: absolute;
    left: 70%;
    top: 16%;
    margin-top: -10rpx;
    margin-left: -10rpx;
  }
  ul li.circle25{
    width: 20rpx;
    height: 20rpx;
    position: absolute;
    left: 90%;
    top: 50%;
    margin-top: -10rpx;
    margin-left: -10rpx;
  }

  .personArea{
    width: 80%;
    margin: 0 auto;
    height: 600rpx;
    position: absolute;
    left: 50%;
    margin-left: -40%;
    top: 50%;
    margin-top: -400rpx;
  }


  .userName{
    display: inline-block;
    width: 100%;
    text-align: center;
    color: rgba(255,255,255,0.3);
    font-size: 16rpx;
    max-width:200rpx;
    overflow:hidden;
    text-overflow:ellipsis;
    white-space:nowrap;
    margin-left:-10rpx;
    margin-top: -25rpx;
    /*position: absolute;*/
    /*left: 0;*/
    /*margin-top: 5rpx;*/
  }
  .c12 ._i-avatar,.c12 .userName{
    display: none;
  }
  .c12{
    background: red;
  }
  .c13{
    background: green;
  }
  .c14{
    background: orange;
  }
  .c15{
    background: blue;
  }
  .c16{
    background: pink;
  }
  .c17{
    background: purple;
  }
  .circle55::after{
    content: '';
    display: inline-block;
    width: 45rpx;
    height:45rpx;
    position: absolute;
    right: 0rpx;
    top: -20rpx;
    z-index:1;
    background: url("../../../static/icon/user0.png");
    background-size: 100% 100%;
  }
  .circleLine{
    position: absolute;
    border-radius: 50%;
    left: 50%;
    top: 50%;
  }
  .circle_big{
    width:570rpx;
    height:570rpx;
    margin-left: -285rpx;
    margin-top: -285rpx;
    border: 1rpx solid rgba(255, 255, 255, 0.1);
  }
  .circle_middle{
    width: 480rpx;
    height:480rpx;
    border: 1rpx solid rgba(255, 255, 255, 0.1);
    margin-left: -240rpx;
    margin-top: -240rpx;
  }
  .circle_small1{
    width: 390rpx;
    height:390rpx;
    border: 1rpx solid rgba(255, 255, 255, 0.1);
    margin-left: -195rpx;
    margin-top: -195rpx;
  }
  .circle_small2{
    width: 300rpx;
    height: 300rpx;
    border: 1rpx solid rgba(255, 255, 255, 0.1);
    margin-left: -150rpx;
    margin-top: -150rpx;
  }
  .circle_small3{
    width: 210rpx;
    height: 210rpx;
    border: 1rpx solid rgba(255, 255, 255, 0.1);
    margin-left: -105rpx;
    margin-top: -105rpx;
  }
  .tip{
    width: 440rpx;
    height: 53rpx;
    border-radius:40rpx ;
    background: #fff;
    position: absolute;
    left:50%;
    margin-left: -220rpx;
    top: 47rpx;
    border:4px solid #999;
    text-align: center;
    font-family:PingFangSC-Regular;
    font-size: 20rpx;
    font-weight:400;
    color:rgba(34,20,50,1);
    line-height: 53rpx;

  }
  .welcome,.guide ,.buy,.share{
    width: 90%;
    margin: 0 auto;
    border: 1px solid transparent;
    margin-bottom: 20rpx;
    background: #ffffff;
    border-radius: 16rpx;
    box-shadow: 0px 0px 2px #ddd;
  }
  .welcome{
    height: 320rpx;
    text-align: center;

  }
  .welcome h4{
    margin-top: 53rpx;
    font-weight: 600;
    color: #333333;
    font-size: 32rpx;
    font-family:NotoSansSyriacEastern;
  }
  .welcome p{
    color: #9A9A9A;
    font-size: 24rpx;
    margin-top: 20rpx;
    margin-bottom: 60rpx;
  }
  .welcome span{
    font-size: 28rpx;
    color: #00BCD4;
  }
  .welcome span i{
    display: inline-block;
    font-size: 18rpx;

  }
  .guide{
    height: 200rpx;
    position: relative;
  }
  .buy{
    height: 320rpx;
  }
  .share{
    height: 92rpx;
    line-height: 92rpx;
    background: #00BCD4;
    color: #ffffff;
    font-size: 34rpx;
  }
  .share i{
    display: inline;
    margin-left: 24rpx;
    margin-right: 24rpx;
  }
  .guide h3,.buy h3{
    color: #333333;
    font-weight: 100;
    font-size: 32rpx;
    margin-top: 33rpx;
    margin-left: 40rpx;
    margin-bottom: 20rpx;
  }
  .guide p{
    color: #9A9A9A;
    font-size: 24rpx;
    margin-left: 40rpx;
  }
  .guide img{
    width: 124rpx;
    height: 124rpx;
    border: 1px solid transparent;
    position: absolute;
    right: 40rpx;
    top: 38rpx;
  }
  .buy img{
    display:block;
    margin: 0 auto;
    width: 600rpx;
    height: 200rpx;
    border: 1px solid transparent;
  }
</style>
