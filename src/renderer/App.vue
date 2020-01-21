<template>
  <div id="app">
    <div class="top">
      <i class="el-icon-refresh icon" @click="update"></i>
    </div>
    <div class="center">
      <el-row>{{city}}</el-row>
      <el-row class="aqi">{{aqi}}</el-row>
      <el-row>{{healthy}}</el-row>
      <el-row>更新时间：{{updateTime}}</el-row>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'weather-widget',
    data () {
      return {
        updateTime: '',
        info: '',
        city: 'City',
        aqi: 0
      }
    },
    computed: {
      healthy () {
        if (this.aqi <= 50) {
          return 'Good'
        } else if (this.aqi <= 100) {
          return 'Moderate'
        } else if (this.aqi <= 150) {
          return 'Unhealthy for Sensitive Groups'
        } else if (this.aqi <= 200) {
          return 'Unhealthy'
        } else if (this.aqi <= 300) {
          return 'Very Unhealthy'
        } else {
          return 'Hazardous'
        }
      }
    },
    methods: {
      update () {
        this.updateTime = new Date()
        var that = this
        console.log('update')
        let url = 'http://api.waqi.info/feed/shenzhen/?token=demo'
        this.$http.get(url)
          .then(function (response) {
            console.log(response.data.data)
            let info = response.data.data
            that.info = info
            that.aqi = info.aqi
            that.city = info.city.name

            console.log(that.info.city.name)
          })
          .catch(function (error) {
            console.log(error)
          })
      }
    }
  }
</script>

<style>
  /* CSS */
  .icon {
    font-size: 30px;
    background-color: greenyellow;
  }
  .text {
    background-color: beige;
    margin-top: 20px;
  }
  .aqi {
    background-color: aqua;
    width: 100%;
    height: 100px;
    font-size: 48px;
    line-height: 100px;
  }
  .center {
    text-align: center
  }
</style>
