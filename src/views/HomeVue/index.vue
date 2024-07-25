<template>
  <div>
    <h2>{{ ttt }}</h2>
    <el-tree :data="data" show-checkbox node-key="id"></el-tree>
    <el-button @click="onSetData">设置数据</el-button>
    <el-button @click="onReset">还原数据</el-button>
    <el-button @click="onTT">setTTT</el-button>
  </div>
</template>

<script>
import dataSource from './dataSource'

export default {
  name: 'HomeVue',
  data() {
    return {
      data: [],
      level: 1,
      maxLevel: 1,
      flatData: [],
      ttt: 'test'
    }
  },
  mounted() {
    this.flatData = this.arrFlat(dataSource)
  },
  methods: {
    onTT() {
      this.ttt = 'nihao'
      this.$nextTick(() => {
        this.ttt = 'hahah'
      })
      const startTime = new Date().getTime()
      while (new Date().getTime() > startTime + 3000) {
        console.log(this.ttt)
      }
    },
    onSetData() {
      console.log(this.data)
      if (this.data.length) return
      // 先简单赋值第一层,后续根据层级分层赋值
      const map = {}
      this.data = this.flatData.filter((item) => {
        return item.level === this.level
      })
      // 每次赋值之后剔除已赋值的数据, 可以节约一点点性能
      this.flatData = this.flatData.filter((item) => item.level !== this.level)
      this.$nextTick(() => {
        this.lazyRender(this.data, map)
      })
    },
    // 递进加载dom, 在上次渲染完成之后继续渲染小部分数据, 拿到本层级内容和对应的映射children
    lazyRender(arr, set) {
      const _this = this
      _this.level++
      console.log(arr)
      console.log(set)
      this.$nextTick(() => {
        // 如果还有下一级那么就继续拿到该层级执行
        if (this.level < this.maxLevel) {
          // 1. 每次拿到该层级的数据之后再剔除该层级的数据, 同时拿到所有ID的Map
          const filterLevelDataSet = {}
          const filterArr = this.flatData.filter((item) => {
            if (item.level === this.level) {
              if (filterLevelDataSet[item.parentId]) {
                filterLevelDataSet[item.parentId].push(item)
              } else {
                filterLevelDataSet[item.parentId] = [item]
              }
              console.log(filterLevelDataSet)
              return true
            }
            return false
          })
          this.flatData = this.flatData.filter(
            (item) => item.level !== this.level
          )
          this.$nextTick(() => this.lazyRender(filterArr, filterLevelDataSet))
        }
      })
    },
    // 数组扁平化, 同时拿到最大递归层级
    arrFlat(arr) {
      const result = []
      const _this = this
      function _deepFlat(arr, level, parentId = 0) {
        if (level > _this.maxLevel) _this.maxLevel = level
        arr.map((item) => {
          if (item.children && item.children.length) {
            _deepFlat(item.children, level + 1, item.id)
          }
          result.push({
            level: level,
            parentId: parentId,
            label: item.label,
            id: item.id
          })
        })
      }
      _deepFlat(arr, 1)
      return result
    },
    onReset() {
      this.data = []
      this.level = 1
      this.flatData = this.arrFlat(dataSource)
    }
  }
}
</script>
