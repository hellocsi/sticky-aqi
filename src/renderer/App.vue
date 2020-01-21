<template>
  <div id="app">
    <div class="top">
      <i class="el-icon-refresh icon" @click="update"></i>
      <i class="el-icon-setting icon" @click="showSetting=true"></i>
    </div>
    <el-drawer
      size="90%"
      title="设置"
      :before-close="closeSetting"
      :visible.sync="showSetting"
      :direction="'btt'"
      :with-header="false">
      <div>设置</div>
      <div>
        请输入Token：
        <el-input v-model="token" placeholder="demo"></el-input>
        <el-button size="mini">确认</el-button>
      </div>
    </el-drawer>
    <div class="center">
      <el-row>{{city}}</el-row>
      <el-row class="aqi">{{aqi}}</el-row>
      <el-row>{{healthy}}</el-row>
      <el-row>更新时间：{{updateTime}}</el-row>
    </div>
  </div>
</template>

<script>
  import qs from 'qs'
  export default {
    name: 'weather-widget',
    data () {
      return {
        token: 'demo',
        updateTime: '',
        info: '',
        city: 'City',
        aqi: 0,
        showSetting: false
      }
    },
    created () {
      this.update()
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
        let param = {
          token: this.token
        }
        let city = 'shenzhen/?'
        let url = 'http://api.waqi.info/feed/' + city + qs.stringify(param)
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
      },
      closeSetting (done) {
        this.update()
        done()
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
