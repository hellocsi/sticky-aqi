<template>
  <div id="app">
    <div class="top">
      <i class="el-icon-refresh icon" @click="update"></i>
      <el-popover
        placement="top"
        width="290"
        v-model="showSetting">
        <p>请输入Token：</p>
        <el-input v-model="token" placeholder="demo"></el-input>
        <div style="text-align: right; margin: 0">
          <a href="http://aqicn.org/data-platform/token/#/" target="_blank">还没有Token？</a>
          <el-button type="primary" size="mini" @click="closeSetting">确定</el-button>
        </div>
        <el-switch
          v-model="setAutoLaunch"
          active-color="#13ce66"
          inactive-color="#ff4949">
        </el-switch>
        <span class="option-text">开机启动</span>
        <i class="el-icon-s-tools icon" slot="reference"></i>
      </el-popover>
      <el-select class="city-select" style="float: right"
        filterable
        v-model="city"
        placeholder="请选择"
        @change="update">
        <el-option-group
          v-for="group in options"
          :key="group.label"
          :label="group.label">
          <el-option
            v-for="item in group.options"
            :key="item.value"
            :label="item.label"
            :value="item">
          </el-option>
        </el-option-group>
      </el-select>
    </div>
    <div class="center content" ref="content">
      <el-row class="city-text big-text">{{city.label}}</el-row>
      <el-popover
        placement="bottom"
        trigger="hover">
        <el-row class="small-text center">空气质量指数<br>Air Quality Index (AQI)</el-row>
        <el-row slot="reference" class="aqi big-text">{{aqi}}</el-row>
      </el-popover>
      <el-row class="middle-text aq-text">{{aqText}}</el-row>
      <el-row class="small-text">更新时间：{{updateTime}}</el-row>
      <a href="http://aqicn.org/city/beijing/"
        target="_blank"
        class="small-text link"
        ref="link"
        @mouseover="linkHover"
        @mouseleave="linkDefault">数据来源：aqicn.org</a>
      <el-row class="notice" v-show="token === 'demo'"> 这是测试界面，请输入正确的Token </el-row>
    </div>
  </div>
</template>

<script>
  import qs from 'qs'
  import { remote } from 'electron'
  export default {
    name: 'weather-widget',
    data () {
      return {
        setAutoLaunch: true,
        token: 'demo',
        updateTime: '',
        city: {
          label: '上海市',
          value: 'Shanghai'
        },
        aqi: 0,
        aqText: '',
        showSetting: false,
        options: [{
          label: '热门城市',
          options: [{
            value: 'Beijing',
            label: '北京市'
          }, {
            value: 'Shanghai',
            label: '上海市'
          }, {
            value: 'Guangzhou',
            label: '广州市'
          }, {
            value: 'Shenzhen',
            label: '深圳市'
          }]
        }, {
          label: '城市名',
          options: [{
            value: 'Chengdu',
            label: '成都市'
          }, {
            value: 'Xian',
            label: '西安市'
          }, {
            value: 'TianJin',
            label: '天津市'
          }, {
            value: 'Dalian',
            label: '大连市'
          }]
        }]
      }
    },
    created () {
      this.update()
      this.$nextTick(() => {
        setInterval(this.update, 1000 * 3600)
      })
      this.setAutoLaunch = remote.app.getLoginItemSettings().openAtLogin
    },
    mounted () {
      if (this.token === 'demo') {
        this.showSetting = true
      }
    },
    watch: {
      setAutoLaunch (newVal, oldVal) {
        remote.app.setLoginItemSettings({
          openAtLogin: newVal
        })
      }
    },
    computed: {
    },
    methods: {
      linkHover () {
        this.$refs.link.style.color = 'blue'
      },
      linkDefault () {
        this.$refs.link.style.color = this.$refs.content.style.color
      },
      changeStyle () {
        if (this.aqi <= 50) {
          this.aqText = '优'
          this.$refs.content.style.background = '#009966'
          this.$refs.content.style.color = '#F2EBEB'
        } else if (this.aqi <= 100) {
          this.aqText = '良'
          this.$refs.content.style.background = 'yellow'
          this.$refs.content.style.color = 'black'
        } else if (this.aqi <= 150) {
          this.aqText = '轻度污染'
          this.$refs.content.style.background = 'orange'
          this.$refs.content.style.color = 'black'
        } else if (this.aqi <= 200) {
          this.aqText = '中度污染'
          this.$refs.content.style.background = 'red'
          this.$refs.content.style.color = '#F2EBEB'
        } else if (this.aqi <= 300) {
          this.aqText = '重度污染'
          this.$refs.content.style.background = 'purple'
          this.$refs.content.style.color = '#F2EBEB'
        } else {
          this.aqText = '严重污染'
          this.$refs.content.style.background = '#7e0023'
          this.$refs.content.style.color = '#F2EBEB'
        }
        this.linkDefault()
      },
      update () {
        let date = new Date()
        let minutes = date.getMinutes() < 10 ? `0${date.getMinutes()}` : `${date.getMinutes()}`
        this.updateTime = `${date.getHours()}:${minutes}`
        var that = this
        console.log('update')
        let param = {
          token: this.token
        }
        let city = `${this.city.value}/?`
        let url = 'http://api.waqi.info/feed/' + city + qs.stringify(param)
        this.$http.get(url)
          .then(function (response) {
            console.log(response.data.data)
            let info = response.data.data
            that.aqi = info.aqi
            that.changeStyle()
            console.log(that.info.city.name)
          })
          .catch(function (error) {
            console.log(error)
          })
      },
      closeSetting (done) {
        this.update()
        this.showSetting = false
      }
    }
  }
</script>

<style>
  /* CSS */
  .notice {
    font-size: 12px;
    color: white;
    margin-top: 5px;
    padding: 2px;
    background-color: gray;
  }
  .link {
    text-decoration: none;
    color: #F2EBEB;
  }
  .top {
    background-color: #D0CC8D;
    height: 48px;
    line-height: 50px;
    -webkit-app-region: drag;
  }
  .icon, .city-select, .no-drag {
    -webkit-app-region: no-drag;
  }
  .city-select {
    width: 150px;
  }
  .icon {
    font-size: 24px;
  }
  .icon:hover {
    color:#2B94D7;
  }
  .big-text, .middle-text, .small-text {
    font-family:"PingFang SC","Hiragino Sans GB","Microsoft YaHei","微软雅黑",Arial,sans-serif
  }
  .big-text {
    font-size: 36px;
  }
  .middle-text {
    font-size: 20px;
  }
  .small-text {
    font-size: 15px;
  }
  .city-text {
    padding-top: 20px;
  }
  .option-text {
    margin-left: 10px;
  }
  .content {
    background-color: #2B94D7;
    height: 252px;
    color: #F2EBEB;
  }
  .aqi {
    width: 70px;
    margin: auto;
    margin-top: 20px;
  }
  .aq-text {
    margin-bottom: 20px;
  }
  .aqi-color {
    background-color: #2B94D7;
  }
  .center {
    text-align: center;
  }
</style>
