<template lang='pug'>
  div(v-bind:class='className' v-bind:style='{height:height,width:width}')
</template>

<script type='text/ecmascript-6'>
  import echarts from 'echarts' // echarts theme
  import {debounce} from '../../../common/utils/index'

  require('echarts/theme/macarons')

  export default {
    name: 'kalix-line-chart',
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
        default: '350px'
      },
      autoResize: {
        type: Boolean,
        default: true
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
      if (this.autoResize) {
        this.__resizeHanlder = debounce(() => {
          if (this.chart) {
            this.chart.resize()
          }
        }, 100)
        window.addEventListener('resize', this.__resizeHanlder)
      }

      // 监听侧边栏的变化
      // const sidebarElm = document.getElementsByClassName('sidebar-container')[0]
      // sidebarElm.addEventListener('transitionend', this.__resizeHanlder)
    },
    beforeDestroy() {
      if (!this.chart) {
        return
      }
      if (this.autoResize) {
        window.removeEventListener('resize', this.__resizeHanlder)
      }

      // const sidebarElm = document.getElementsByClassName('sidebar-container')[0]
      // sidebarElm.removeEventListener('transitionend', this.__resizeHanlder)

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
      /* setOptions({expectedData, actualData} = {}) {
        this.chart.setOption({
          xAxis: {
            data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
            boundaryGap: false,
            axisTick: {
              show: false
            }
          },
          grid: {
            left: 10,
            right: 10,
            bottom: 20,
            top: 30,
            containLabel: true
          },
          tooltip: {
            trigger: 'axis',
            axisPointer: {
              type: 'cross'
            },
            padding: [5, 10]
          },
          yAxis: {
            axisTick: {
              show: false
            }
          },
          legend: {
            data: ['expected', 'actual']
          },
          series: [{
            name: 'expected',
            itemStyle: {
              normal: {
                color: '#FF005A',
                lineStyle: {
                  color: '#FF005A',
                  width: 2
                }
              }
            },
            smooth: true,
            type: 'line',
            data: expectedData,
            animationDuration: 2800,
            animationEasing: 'cubicInOut'
          }, {
            name: 'actual',
            smooth: true,
            type: 'line',
            itemStyle: {
              normal: {
                color: '#3888fa',
                lineStyle: {
                  color: '#3888fa',
                  width: 2
                },
                areaStyle: {
                  color: '#f3f8ff'
                }
              }
            },
            data: actualData,
            animationDuration: 2800,
            animationEasing: 'quadraticOut'
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
