<template>
  <div class="page">

    <div class="personDiv">
      <img src="../../../static/icon/personBg.jpg" alt="" class="bg">
      <div class="personImg">
        <i-avatar  v-bind:src="person.imageurl" size="huge" i-class="personavatar "></i-avatar>
        <!--<i-avatar v-else v-bind:src="person.imageurl" size="huge" i-class="personavatar_off" iimage="imagegray"></i-avatar>-->
      </div>
      <div class="editPerson">
        <img src="../../../static/icon/bj.png" alt="" class="bj" @click="personEdit">
      </div>

      <h4 class="nickname">{{person.nickname}}</h4>
      <div v-if="current">
        <p class="onLine"> <span v-if="current.onoff==1" class="online">在线</span>
          <span v-else class="online">离线</span></p>
        <p class="current">
          <span v-if="current.onoff==1">最近上线时间：</span>
          <span v-else>最近下线时间：</span>
          <span>{{current.timeStr}}</span></p>
      </div>

      <h1 @click="setFoucus">{{FoucusPerson.uid==person.uid?'已关注':'关注'}}</h1>
    </div>
    <div class="devDiv">
      <ul v-if="dev.length>0">
        <li v-for="(item,index) in dev" :key="item.did" @click="devSelect(item)"
            :class="item.block==1?'blockIcon':item.plan==true?'planIcon':'nothing'">
          <Avatar :pid="item.pid" :online="item.online" :dtype="item.dtype" iclass="dev_avatar"></Avatar>
          <p class="weui-grid__label" v-if="item.dname!=''" style="color: #999999;margin-top: 0px;font-size: 22rpx">{{ item.dname }}</p>
          <p class="weui-grid__label" v-else-if="item.hostname!=''" style="color: #999999;margin-top: 0px;font-size: 22rpx">{{ item.hostname }}</p>
          <p class="weui-grid__label" v-else-if="item.netbios!=''" style="color: #999999;margin-top: 0px;font-size: 22rpx">{{ item.netbios }}</p>
          <p class="weui-grid__label" v-else-if="item.oui!=''" style="color: #999999;margin-top: 0px;font-size: 22rpx">{{ item.oui }}</p>
        </li>
      </ul>
      <p v-else class="noData">暂无设备</p>
    </div>
    <div class="presentDiv" :style="{minHeight: height+'px'}">
      <ul v-if="timeLine.length>0">
        <li v-for="(item,index) in timeLine" :key="index" >
          <i-avatar v-if="item.onoff==1" v-bind:src="person.imageurl" size="default" i-class="personavatar presentUImg "></i-avatar>
          <i-avatar v-else v-bind:src="person.imageurl" size="default" i-class="personavatar_off presentUImg" iimage="imagegray"></i-avatar>
          <Avatar :online="item.onoff==1" :dtype="item.dtype" iclass="dev_avatar presentDImg"></Avatar>
          <p class="presentUName"><span>{{person.nickname}}</span><span style="padding: 0 5rpx">/</span>  <span >{{ item.dname }}</span></p>
          <p  class="presentTime">
            <span v-if="item.onoff==1">上线时间：</span>
            <span v-else>下线时间：</span>
            <span>{{item.timeStr}}</span>
          </p>
        </li>
      </ul>
      <p v-else class="noData">暂无数据</p>
    </div>

    <i-modal   :visible="visible1" :show-cancel="false"
               :show-ok="false">
      <h3 class="modalTitle">关注成功</h3>
      <p>您已成功关注了 <span class="usernickname">[{{person.nickname}}]</span></p>
      <p><span class="usernickname">{{person.nickname}}</span>的网络使用情况将会显示在</p>
      <p>您的关注页面，赶快去看看吧</p>
      <span class="goFoucusBtn" @click="goFoucus">去查看</span>
    </i-modal>
    <i-modal   :visible="visible2"  :show-cancel="false"
               :show-ok="false" i-class="modal2" >
      <h3 class="modalTitle">确认关注</h3>
      <p>您已关注了 <span class="usernickname">[{{FoucusPerson.nickname}}]</span></p>
      <p>是否要更换所关注的人吗</p>
      <span class="modalBtn changeFocusBtn" @click="changeFocus">确认更换</span><span class="modalBtn cancelBtn" @click="cancelChange">取消</span>
    </i-modal>
  </div>
</template>

<script>
  import Avatar from '../../components/avatar'
  export default {
    data () {
      return {
        person: {},
        visible1: false,
        visible2: false,
        dev: [],
        timeLine: '',
        current: '',
        foucsPerson:'',
        height:'',
        FoucusPerson:'',
        foucsBtn: false
      }
    },
    components: {
      Avatar
    },
    onLoad () {
      this.person = wx.getStorageSync('personEdit')
      this.visible1 = false
      this.visible2 = false
    },
    onShow () {
      this.person = wx.getStorageSync('personEdit')
      this.getdev()
      this.getPresent()
      this.height = wx.getSystemInfoSync().windowHeight - 470
      this.getFoucusPerson()
      this.visible1 = false
      this.visible2 = false
    },
    onReady () {
      this.visible1 = false
      this.visible2 = false
    },
    methods: {
      goFoucus () {
        this.visible2 = false
        this.visible1 = false
        wx.switchTab({
          url: '/pages/box/mycare'
        })
      },
      cancelChange () {
        this.visible2 = false
        this.visible1 = false
      },
      changeFocus () {
        var uid = this.person.uid
        var param = {
          uid: uid,
          care: true
        }
        this.$api.put('/person/' + uid + '/care', param, null, r => {
          wx.showToast({
            title: '更换成功',
            icon: 'success',
            duration: 1000
          })
        })
        this.visible2 = false
        this.visible1 = false
      },
      setFoucus () {
        var _this = this
        console.log(_this.FoucusPerson)
        if (_this.foucsBtn) {
          console.log(_this.FoucusPerson!== ''&& _this.FoucusPerson!== null)
          if (_this.FoucusPerson!== ''&& _this.FoucusPerson!== null) {
            _this.visible2 = true
          } else {
            _this.visible1 = true
            var uid = _this.person.uid
            var param = {
              uid: uid,
              care: true
            }
            _this.$api.put('/person/' + uid + '/care', param, null, r => {
            })
          }
        }
      },
      getdev () {
        var _this = this
        _this.dev = []
        this.$api.get('/dev', {'online': 0, 'pid': this.person.uid}, null, r => {
          _this.dev = r.data
          if (_this.dev.length > 0) {
            _this.dev.forEach(function (v, k) {

              _this.$api.get('/plan/' + v.dmac, null, null, r1 => {
                if (r1.data != '' && r1.data != null) {
                  v['plan'] = true
                } else {
                  v['plan'] = false
                }
              })
            })
          } else {
            // this.dhcp = []
          }
        })
      },
      devSelect (item) {
        if (item.online) {
          item.color = 'green'
        } else {
          item.color = 'grey'
        }
        item.person['imageurl'] = this.$api.ImgName(item.person.uicon)
        item['firstTimeStr'] = this.$api.formatDate('yyyy-MM-dd hh:mm:ss', new Date(item.firsttime * 1000));
        item['lastTimeStr'] = this.$api.formatDate('yyyy-MM-dd hh:mm:ss', new Date(item.lasttime * 1000));
        wx.setStorageSync('devdata', item)
        wx.navigateTo({
          url: '/pages/box/devdetail'
        })
      },
      personEdit () {
        wx.setStorageSync('person',this.person)
        wx.navigateTo({
          url: '/pages/box/personupdate'
        })
      },
      getPresent () {
        this.timeLine = []
        var pid = this.person.uid
        var param = {
          pid: pid
        }
        var _this = this
        this.$api.get('/present/person', param, null, r => {
          _this.timeLine = r.data
          _this.timeLine.forEach(function (v, k) {
            v['timeStr'] = _this.$api.formatDate('yyyy-MM-dd hh:mm:ss', new Date(v.time * 1000))
          })
          _this.current = _this.timeLine[0]
          console.log(_this.timeLine)
        })

      },
      getFoucusPerson () {
        var _this = this
        this.$api.get('/person-cared', null, null, r => {
          if (r.data !== '' && r.data !== null) {
            _this.FoucusPerson = r.data
            console.log(_this.FoucusPerson)
            console.log(_this.person)
            if (_this.FoucusPerson.uid == _this.person.uid) {
              _this.foucsBtn = false
            } else {
              _this.foucsBtn = true
            }
          }else {
            _this.foucsBtn = true
          }
        })
      }
    }
  }
</script>

<style scoped>
  .personDiv{
    width: 100%;
    height: 600rpx;
    position: relative;
  }
  .personDiv .bg{
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 600rpx;
  }
  .personDiv .personImg{
    width: 160rpx;
    height: 160rpx;
    position: absolute;
    left: 50%;
    top: 90rpx;
    margin-left: -80rpx;
  }
  .personDiv h4.nickname{
    width: 100%;
    font-size: 32rpx;
    color: #fff;
    font-family:Helvetica;
    text-align: center;
    height: 40rpx;
    line-height: 40rpx;
    position: absolute;
    left: 0;
    top: 270rpx;
  }
  .personDiv p.onLine{
    width: 100%;
    font-size: 24rpx;
    color: #fff;
    font-family:Helvetica;
    text-align: center;
    height: 40rpx;
    line-height: 40rpx;
    position: absolute;
    left: 0;
    top: 350rpx;
  }
  .personDiv p.current{
    width: 100%;
    font-size: 24rpx;
    color: #fff;
    font-family:Helvetica;
    text-align: center;
    height: 40rpx;
    line-height: 40rpx;
    position: absolute;
    left: 0;
    top: 450rpx;
  }
  .personDiv p span{
    margin: 0 5rpx;
  }
  .personDiv h1{
    width: 330rpx;
    height: 84rpx;
    background: #00BCD4;
    border-radius:40rpx ;
    position: absolute;
    left: 50%;
    bottom:0;
    margin-left: -165rpx;
    margin-bottom: -42rpx;
    text-align: center;
    line-height: 84rpx;
    font-size: 32rpx;
    color: #fff;
    font-family:Helvetica;
    -moz-box-shadow: 0rpx 5rpx 10rpx #00BCD4; /* 老的 Firefox */
    box-shadow: 0rpx 5rpx 10rpx #00BCD4;
  }
  .devDiv{
    width: 100%;
    height: 210rpx;
    background: #fff;
    border-bottom: 25rpx solid #F0F0F0;
  }
  .devDiv ul{
    width: 360rpx;
    height: 130rpx;
    margin: 0 auto;
    padding-top: 80rpx;
    text-align: center;
  }
  .devDiv ul li {
    position: relative;
    display: inline-block;
    width: 100rpx;
    text-align: center;
    margin: 0 10rpx;
  }
  .devDiv ul li p{
    overflow:hidden;
    text-overflow:ellipsis;
    white-space:nowrap
  }

  .presentDiv{
    width: 100%;
    height: auto;
    background: #fff;
  }
  .presentDiv ul li{
    height: 140rpx;
    width: 100%;
    position: relative;
  }
  p.presentUName{
    width: 400rpx;
    position: absolute;
    left: 300rpx;
    top: 35rpx;
    overflow:hidden;
    text-overflow:ellipsis;
    white-space:nowrap;

  }
  .presentUName span{
    float: left;
  }
  .presentUName span:first-child{
    display: inline-block;
    max-width: 180rpx;
    overflow:hidden;
    text-overflow:ellipsis;
    white-space:nowrap
  }
  .presentUName span:last-child{
    display: inline-block;
    max-width: 260rpx;
    overflow:hidden;
    text-overflow:ellipsis;
    white-space:nowrap
  }
  p.presentTime{
    position: absolute;
    left: 300rpx;
    top: 80rpx;
    font-size: 24rpx;
    color: #999;

  }
  .presentDiv ul li:after{
    content: '';
    display: inline-block;
    width: 2rpx;
    height:110rpx;
    background: #D8D8D8;
    position: absolute;
    left: 240rpx;
    top: 115rpx;
  }
  .presentDiv ul li:last-child:after{
    display: none;
  }
  .bj{
    width:36rpx;
    height:36rpx;
    position: absolute;
    left: 50%;
    margin-left: -18rpx;
    top: 18rpx;
  }

  .online {
    border: 1px solid #00BCD4;
    color: #00BCD4;
    padding: 5rpx 10rpx;
    border-radius: 20rpx;
    margin-right: 20rpx;
  }

  .goFoucusBtn {
    display: inline-block;
    width: 220rpx;
    height: 60rpx;
    border-radius: 40rpx;
    background: #00BCD4;
    text-align: center;
    line-height: 60rpx;
    color: #fff;
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
  .planIcon::after{
    content: '';
    display: inline-block;
    width: 30rpx;
    height:30rpx;
    position: absolute;
    right:10rpx;
    top: 50rpx;
    z-index:1000;
    background: url("../../../static/icon/time.png");
    background-size: 100% 100%;
  }
  .blockIcon::after{
    content: '';
    display: inline-block;
    width: 30rpx;
    height:30rpx;
    position: absolute;
    right:10rpx;
    top: 50rpx;
    z-index:1000;
    background: url("../../../static/icon/onNet.png");
    background-size: 100% 100%;
  }
  .editPerson{
    width: 75rpx;
    height: 72rpx;
    border-radius:50% 0 0 50% ;
    background: #fff;
    position: absolute;
    right: 0;
    top: 30rpx;
  }
  .usernickname{
    color: #00BCD4;
  }
  .noData{
    text-align: center;
    line-height: 210rpx;
    height: 210rpx;
    background: #fff;
    color: #999;
    font-size: 24rpx;
  }

</style>
