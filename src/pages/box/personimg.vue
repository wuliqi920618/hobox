<template>
  <div class="page">
    <div class="weui-grids">
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 0 }" @click="iconChange('0')">
            <img src="https://www.hobox.com.cn:8082/guide/p_boy.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 1 }" @click="iconChange('1')">
            <img src="https://www.hobox.com.cn:8082/guide/p_girl.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 2 }" @click="iconChange('2')">
            <img src="https://www.hobox.com.cn:8082/guide/p_man.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 3 }" @click="iconChange('3')">
            <img src="https://www.hobox.com.cn:8082/guide/p_woman.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 4 }" @click="iconChange('4')">
            <img src="https://www.hobox.com.cn:8082/guide/p_man_old.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 5 }" @click="iconChange('5')">
            <img src="https://www.hobox.com.cn:8082/guide/p_woman_old.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 6 }" @click="iconChange('6')">
            <img src="https://www.hobox.com.cn:8082/guide/p_home.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 7 }" @click="iconChange('7')">
            <img src="https://www.hobox.com.cn:8082/guide/p_boy_2.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 8 }" @click="iconChange('8')">
            <img src="https://www.hobox.com.cn:8082/guide/p_boy_3.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 9 }" @click="iconChange('9')">
            <img src="https://www.hobox.com.cn:8082/guide/p_boy_4.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 10 }" @click="iconChange('10')">
            <img src="https://www.hobox.com.cn:8082/guide/p_boy_5.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 11 }" @click="iconChange('11')">
            <img src="https://www.hobox.com.cn:8082/guide/p_girl_2.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 12 }" @click="iconChange('12')">
            <img src="https://www.hobox.com.cn:8082/guide/p_girl_3.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 13 }" @click="iconChange('13')">
            <img src="https://www.hobox.com.cn:8082/guide/p_girl_4.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 14 }" @click="iconChange('14')">
            <img src="https://www.hobox.com.cn:8082/guide/p_girl_5.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 15 }" @click="iconChange('15')">
            <img src="https://www.hobox.com.cn:8082/guide/p_man_2.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 16 }" @click="iconChange('16')">
            <img src="https://www.hobox.com.cn:8082/guide/p_man_3.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 17 }" @click="iconChange('17')">
            <img src="https://www.hobox.com.cn:8082/guide/p_man_4.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 18 }" @click="iconChange('18')">
            <img src="https://www.hobox.com.cn:8082/guide/p_woman_2.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 19 }" @click="iconChange('19')">
            <img src="https://www.hobox.com.cn:8082/guide/p_woman_3.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 20 }" @click="iconChange('20')">
            <img src="https://www.hobox.com.cn:8082/guide/p_woman_4.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 21 }" @click="iconChange('21')">
            <img src="https://www.hobox.com.cn:8082/guide/p_man_old_2.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 22 }" @click="iconChange('22')">
            <img src="https://www.hobox.com.cn:8082/guide/p_man_old_3.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 23 }" @click="iconChange('23')">
            <img src="https://www.hobox.com.cn:8082/guide/p_man_old_4.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 24 }" @click="iconChange('24')">
            <img src="https://www.hobox.com.cn:8082/guide/p_woman_old_2.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 25 }" @click="iconChange('25')">
            <img src="https://www.hobox.com.cn:8082/guide/p_woman_old_3.png" class="personImg"/>
          </div>
        </div>
      </block>
      <block>
        <div class="weui-grid">
          <div class="placeholder" :class="{ bordercolor: uicon == 26 }" @click="iconChange('26')">
            <img src="https://www.hobox.com.cn:8082/guide/p_woman_old_4.png" class="personImg"/>
          </div>
        </div>
      </block>
    </div>
  </div>
</template>

<script>
    export default {
      data () {
        return {
          uicon: 0
        }
      },
      onLoad () {
        this.uicon = wx.getStorageSync('uicon')
      },
      methods: {
        iconChange (uicon) {
          this.uicon = parseInt(uicon)
          wx.setStorageSync('uicon', this.uicon)
          wx.navigateBack()
        }
      }
    }
</script>

<style scoped>
  .page{
    margin-top: 15px;
  }
  .placeholder {
    margin: 1px;
    height: 42px;
    text-align: center;
    line-height: 42px;
    background-color: #ffffff;
    border: 2px solid transparent;
    -webkit-border-radius: 5px;
  }
  .bordercolor{
    border: 2px solid #75ce66 !important;
  }
  .personImg{
    width: 42px;
    height: 42px;
  }
  .weui-grids{
    border-top: 0px;
    border-left: 0px;
  }
  .weui-grid{
    width: 25%;
    padding: 0px 0px;
    border-right: 0px;
    border-bottom: 0px;
  }
</style>
