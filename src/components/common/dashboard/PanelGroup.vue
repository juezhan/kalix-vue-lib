<template lang="pug">
  el-row.panel-group(v-bind:gutter="40")
    template(v-for="col in cols")
      el-col.card-panel-col(v-bind:xs="12" v-bind:sm="12" v-bind:lg="6")
        div.card-panel(v-on:click="handleSetLineChartData(col.key)")
          div.card-panel-icon-wrapper(v-bind:class="col.iconName")
            svg-icon(v-bind:icon-class="col.iconName" class-name="card-panel-icon")
          div.card-panel-description
            div.card-panel-text {{col.text}}
            count-to.card-panel-num(v-bind:startVal="col.startVal" v-bind:endVal="col.endVal" v-bind:duration="col.duration")
</template>

<script type="text/ecmascript-6">
  import CountTo from 'vue-count-to'
  import SvgIcon from './SvgIcon'

  export default {
    name: 'kalix-panel-group',
    props: {
      targetURL: {
        default: ''
      }
    },
    data() {
      return {
        /*
        cols: [{
          key: 'newVisitis',
          iconName: 'peoples',
          iconBackgroundColor: '#40c9c6',
          text: 'New Visits',
          startVal: 0,
          endVal: 102400,
          duration: 2600
        }, {
          key: 'messages',
          iconName: 'message',
          iconBackgroundColor: '#36a3f7',
          text: 'Messages',
          startVal: 0,
          endVal: 81212,
          duration: 3000
        }, {
          key: 'purchases',
          iconName: 'money',
          iconBackgroundColor: '#f4516c',
          text: 'Purchases',
          startVal: 0,
          endVal: 9280,
          duration: 3200
        }, {
          key: 'shoppings',
          iconName: 'shoppingCard',
          iconBackgroundColor: '#34bfa3',
          text: 'Shoppings',
          startVal: 0,
          endVal: 13600,
          duration: 3600
        }]
        */
        cols: []
      }
    },
    components: {
      CountTo,
      SvgIcon
    },
    created() {
      // let styles = []
      // this.cols.forEach(item => {
      //   styles.push(
      //     `.card-panel .${item.iconName}{color:${item.iconBackgroundColor}}` +
      //     `.card-panel:hover .${item.iconName}{background: ${item.iconBackgroundColor}}`)
      // })
      // document.write(`<style>${styles.join()}</style>`)
    },
    mounted() {
      this.fentch()
    },
    watch: {
      targetURL: {
        handler(val) {
          this.getData()
        }
      }
    },
    methods: {
      // 组件初始化
      fentch() {
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
            this.cols = res.data.data
            this.$emit('handleGetDataComplete', this.cols[0].key)
          }
        })
      },
      handleSetLineChartData(key) {
        this.$emit('handleSetLineChartData', key)
      }
    }
  }
</script>

<style type="text/stylus" lang="stylus" scoped>
  .panel-group {
    margin-top: 18px;
    .card-panel-col {
      margin-bottom: 32px;
    }
    .card-panel {
      height: 108px;
      cursor: pointer;
      font-size: 12px;
      position: relative;
      overflow: hidden;
      color: #666;
      background: #fff;
      box-shadow: 4px 4px 40px rgba(0, 0, 0, .05);
      border-color: rgba(0, 0, 0, .05);
      &:hover {
        .card-panel-icon-wrapper {
          color: #fff;
        }
        .peoples {
          background: #40c9c6;
        }
        .message {
          background: #36a3f7;
        }
        .money {
          background: #f4516c;
        }
        .shoppingCard {
          background: #34bfa3;
        }
      }
      .peoples {
        color: #40c9c6;
      }
      .message {
        color: #36a3f7;
      }
      .money {
        color: #f4516c;
      }
      .shoppingCard {
        color: #34bfa3;
      }
      .card-panel-icon-wrapper {
        float: left;
        margin: 14px 0 0 14px;
        padding: 16px;
        transition: all 0.38s ease-out;
        border-radius: 6px;
      }
      .card-panel-icon {
        float: left;
        font-size: 48px;
      }
      .card-panel-description {
        float: right;
        font-weight: bold;
        margin: 26px;
        margin-left: 0px;
        .card-panel-text {
          line-height: 18px;
          color: rgba(0, 0, 0, 0.45);
          font-size: 16px;
          margin-bottom: 12px;
        }
        .card-panel-num {
          font-size: 20px;
        }
      }
    }
  }
</style>
