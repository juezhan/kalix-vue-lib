<!--
描述：basetree 树形控件
     使用场景：如职务管理等，先选取组织机构树，再操作职务信息。
     通过树形控件选定节点，对表进行增删改查信息操作，即表中有某一字段为树形结构，该树形结构字段需单独建表管理，对表进行维护操作时可使用该控件。
     使用方法见docs/component/tree.md
开发人：hqj
开发日期：2017年8月23日
修改人：hqj
修改日期：2017年12月01日
-->

<template lang="pug">
  div.kalix-search
    div.kalix-search-hd
      i.tit_icon.iconfont.icon-organization
      | {{treeTitle}}
    div.kalix-search-bd
      el-input.kalix-search-input(placeholder="输入关键字进行过滤" v-model="filterText")
      div.kalix-tree-wrapper
        el-tree.filter-tree(v-bind:data="treeData" v-bind:props="defaultProps" node-key="id" accordion highlight-current
        v-bind:filter-node-method="filterNode" v-on:node-click="handleNodeClick" ref="baseTree")
</template>

<script type="text/ecmascript-6">
  export default {
    name: 'kalix-tree',
    props: {
      treeTitle: {
        default: '组织机构树'
      },
      treeDataURL: {
        default: ''
      },
      parentNodeId: {
        default: -1
      }
    },
    data () {
      return {
        filterText: '',
        treeData: [],
        defaultProps: {
          children: 'children',
          label: 'name'
        }
      }
    },
    mounted () {
      this.fentch()
    },
    watch: {
      filterText (val) {
        this.$refs.baseTree.filter(val)
      }
    },
    methods: {
      // 初始化
      fentch () {
        this.getData()
      },
      // 获取数据
      getData() {
        if (!this.treeDataURL) {
          return
        }
        let url = ''
        if (this.parentNodeId === -1) {
          url = this.treeDataURL
        } else {
          url = this.treeDataURL + '/' + this.parentNodeId
        }
        this.axios.request({
          method: 'GET',
          url: url,
          params: {}
        }).then(res => {
          if (res.data.children) {
            this.treeData = res.data.children
          }
          // 加载数据后自动选中第一个节点
          this.$nextTick(() => {
            const firstNode = document.querySelector('.el-tree-node')
            if (firstNode) {
              firstNode.click()
            }
          })
        })
      },
      filterNode (value, data) {
        if (!value) {
          return true
        } else {
          return data.name.indexOf(value) !== -1
        }
      },
      handleNodeClick (data) {
        this.$emit('nodeClick', data)
      }
    }
  }
</script>
