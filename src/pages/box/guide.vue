<template>
  <div class="page" style="background: #fff">
    <div class="guide"  style="background: #fff">
      <img src="https://www.hobox.com.cn:8082/guide/guide_01.jpg" alt="" style="height: 575px" >
      <img src="https://www.hobox.com.cn:8082/guide/guide_02.jpg" alt="" style="height: 575px">
      <img src="https://www.hobox.com.cn:8082/guide/guide_03.jpg" alt="" style="height: 575px">
      <img src="https://www.hobox.com.cn:8082/guide/guide_04.jpg" alt="" style="height: 575px">
      <img src="https://www.hobox.com.cn:8082/guide/guide_05.jpg" alt="" style="height: 575px">
      <img src="https://www.hobox.com.cn:8082/guide/guide_06.jpg" alt="" style="height: 575px">
      <img src="https://www.hobox.com.cn:8082/guide/guide_07.jpg" alt="" style="height: 575px">
      <img src="https://www.hobox.com.cn:8082/guide/guide_08.jpg" alt="" style="height: 575px">
      <img src="https://www.hobox.com.cn:8082/guide/guide_09.jpg" alt="" style="height: 575px">
    </div>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        height: 0
      }
    },
    onShow () {
      var _this = this
      wx.getSystemInfo({
        success: function (res) {
          _this.height = 575
          console.log(_this.height + 'px')
        }
      })
    },
    methods: {}
  }
</script>

<style scoped>
  .page {
  }
.guide{
  width: 100%;
  height: auto;
}
  img{
    width: 100%;
    display:block;
  }
</style>
