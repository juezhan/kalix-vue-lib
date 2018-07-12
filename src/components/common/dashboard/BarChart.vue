<template lang="pug">
  div(v-bind:class="className" v-bind:style="{height:height,width:width}")
</template>

<script type="text/ecmascript-6">
  import echarts from 'echarts'
  import {debounce} from '../../../common/utils/index'

  require('echarts/theme/macarons') // echarts theme

  // const animationDuration = 6000

  export default {
    name: 'kalix-bar-chart',
    props: {
      className: {
        type: String,
        default: 'chart'
      },
      width: {
        type: String,
        default: '100%'
      },
      height: {
        type: String,
        default: '300px'
      },
      targetURL: {
        type: String,
        default: ''
      }
    },
    data() {
      return {
        chart: null
      }
    },
    mounted() {
      this.initChart()
      this.__resizeHanlder = debounce(() => {
        if (this.chart) {
          this.chart.resize()
        }
      }, 100)
      window.addEventListener('resize', this.__resizeHanlder)
    },
    beforeDestroy() {
      if (!this.chart) {
        return
      }
      window.removeEventListener('resize', this.__resizeHanlder)
      this.chart.dispose()
      this.chart = null
    },
    watch: {
      targetURL: {
        handler(val) {
          this.getData()
        }
      }
    },
    methods: {
      /* setOptions() {
        this.chart.setOption({
          tooltip: {
            trigger: 'axis',
            axisPointer: { // 坐标轴指示器，坐标轴触发有效
              type: 'shadow' // 默认为直线，可选为：'line' | 'shadow'
            }
          },
          grid: {
            top: 10,
            left: '2%',
            right: '2%',
            bottom: '3%',
            containLabel: true
          },
          xAxis: [{
            type: 'category',
            data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
            axisTick: {
              alignWithLabel: true
            }
          }],
          yAxis: [{
            type: 'value',
            axisTick: {
              show: false
            }
          }],
          series: [{
            name: 'pageA',
            type: 'bar',
            stack: 'vistors',
            barWidth: '60%',
            data: [79, 52, 200, 334, 390, 330, 220],
            animationDuration
          }, {
            name: 'pageB',
            type: 'bar',
            stack: 'vistors',
            barWidth: '60%',
            data: [80, 52, 200, 334, 390, 330, 220],
            animationDuration
          }, {
            name: 'pageC',
            type: 'bar',
            stack: 'vistors',
            barWidth: '60%',
            data: [30, 52, 200, 334, 390, 330, 220],
            animationDuration
          }]
        })
      }, */
      setOptions(option) {
        let op = JSON.parse(option)
        this.chart.setOption(op, true)
      },
      initChart() {
        this.chart = echarts.init(this.$el, 'macarons')
        this.getData()
      },
      // 获取数据
      getData() {
        if (!this.targetURL) {
          return
        }
        let url = this.targetURL
        this.axios.request({
          method: 'GET',
          url: url,
          params: {}
        }).then(res => {
          if (res.data.data) {
            this.setOptions(res.data.data[0])
          }
        })
      }
    }
  }
</script>
