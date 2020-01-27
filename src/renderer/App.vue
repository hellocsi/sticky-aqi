<template>
  <div id="app">
    <div class="top">
      <i class="el-icon-refresh icon" @click="update"></i>
      <i class="el-icon-s-tools icon" @click="showSetting=true"></i>
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
    <el-drawer
      size="85%"
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
    <div class="center content" ref="content">
      <el-row class="city-text">{{city.label}}</el-row>
      <el-row class="aqi">{{aqi}}</el-row>
      <el-row>{{aqText}}</el-row>
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
    },
    computed: {
    },
    methods: {
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
        done()
      }
    }
  }
</script>

<style>
  /* CSS */
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
  .city-text {
    font-size: 36px;
    padding-top: 20px;
  }
  .content {
    background-color: #2B94D7;
    height: 252px;
    color: #F2EBEB;
  }
  .aqi {
    width: 100%;
    height: 100px;
    font-size: 48px;
    line-height: 100px;
  }
  .aqi-color {
    background-color: #2B94D7;
  }
  .center {
    text-align: center
  }
</style>
