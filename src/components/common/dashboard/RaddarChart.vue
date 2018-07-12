<template lang="pug">
  div(v-bind:class="className" v-bind:style="{height:height,width:width}")
</template>

<script type="text/ecmascript-6">
  import echarts from 'echarts'
  import {debounce} from '../../../common/utils/index'

  require('echarts/theme/macarons') // echarts theme

  export default {
    name: 'kalix-raddar-chart',
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
          radar: {
            radius: '66%',
            center: ['50%', '42%'],
            splitNumber: 8,
            splitArea: {
              areaStyle: {
                color: 'rgba(127,95,132,.3)',
                opacity: 1,
                shadowBlur: 45,
                shadowColor: 'rgba(0,0,0,.5)',
                shadowOffsetX: 0,
                shadowOffsetY: 15
              }
            },
            indicator: [
              {name: 'Sales', max: 10000},
              {name: 'Administration', max: 20000},
              {name: 'Information Techology', max: 20000},
              {name: 'Customer Support', max: 20000},
              {name: 'Development', max: 20000},
              {name: 'Marketing', max: 20000}
            ]
          },
          legend: {
            left: 'center',
            bottom: '10',
            data: ['Allocated Budget', 'Expected Spending', 'Actual Spending']
          },
          series: [{
            type: 'radar',
            symbolSize: 0,
            areaStyle: {
              normal: {
                shadowBlur: 13,
                shadowColor: 'rgba(0,0,0,.2)',
                shadowOffsetX: 0,
                shadowOffsetY: 10,
                opacity: 1
              }
            },
            data: [
              {
                value: [5000, 7000, 12000, 11000, 15000, 14000],
                name: 'Allocated Budget'
              },
              {
                value: [4000, 9000, 15000, 15000, 13000, 11000],
                name: 'Expected Spending'
              },
              {
                value: [5500, 11000, 12000, 15000, 12000, 12000],
                name: 'Actual Spending'
              }
            ],
            animationDuration: 3000
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
