<template lang="pug">
  div(v-bind:class="className" v-bind:style="{height:height,width:width}")
</template>

<script type="text/ecmascript-6">
  import echarts from 'echarts'
  import {debounce} from '../../../common/utils/index'

  require('echarts/theme/macarons') // echarts theme

  export default {
    name: 'kalix-pie-chart',
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
            trigger: 'item',
            formatter: '{a} <br/>{b} : {c} ({d}%)'
          },
          legend: {
            left: 'center',
            bottom: '10',
            data: ['Industries', 'Technology', 'Forex', 'Gold', 'Forecasts']
          },
          calculable: true,
          series: [
            {
              name: 'WEEKLY WRITE ARTICLES',
              type: 'pie',
              roseType: 'radius',
              radius: [15, 95],
              center: ['50%', '38%'],
              data: [
                {value: 320, name: 'Industries'},
                {value: 240, name: 'Technology'},
                {value: 149, name: 'Forex'},
                {value: 100, name: 'Gold'},
                {value: 59, name: 'Forecasts'}
              ],
              animationEasing: 'cubicInOut',
              animationDuration: 2600
            }
          ]
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
