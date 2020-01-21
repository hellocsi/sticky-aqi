<template>
  <div id="app">
    <div class="top">
      <el-button @click="update">更新</el-button>
      <span>更新时间：{{updateTime}}</span>
    </div>
    <div class="text">
      <el-row>{{info.city}}</el-row>
      <el-row>{{info.iaqi}}</el-row>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'weather-widget',
    data () {
      return {
        updateTime: '',
        info: ''
      }
    },
    methods: {
      update () {
        this.updateTime = new Date()
        var that = this
        console.log('update')
        let url = 'http://api.waqi.info/feed/shanghai/?token=demo'
        this.$http.get(url)
          .then(function (response) {
            console.log(response.data.data)
            that.info = response.data.data
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
  .top {
    background-color: pink;
  }
  .text {
    background-color: beige;
    margin-top: 20px;
  }
</style>
