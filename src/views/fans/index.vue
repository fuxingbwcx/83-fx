<template>
  <el-card>
    <bread-crumb slot="header">
      <template slot="title">图文数据</template>
    </bread-crumb>
    <el-row type='flex' justify="space-between">
      <div ref="workChart" class="echarts"></div>
      <div ref="lifeChart" class="echarts"></div>
    </el-row>
  </el-card>
</template>

<script>
import echarts from 'echarts'
export default {
  data () {
    return {
      workData: [820, 932, 901, 934, 1290, 1330, 1320], // 初始值
      lifeData: [10, 52, 200, 334, 390, 330, 220] // 初始值
    }
  },
  methods: {
    //   更新数据
    updateData () {
      this.lifeData = this.lifeData.map(item => {
        return item * (Math.random() + 0.7)
      })
      this.workData = this.workData.map(item => {
        return item * (Math.random() + 0.7)
      })
    },
    setWorkOption () {
      this.workChart.setOption({
        xAxis: {
          type: 'category',
          data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
        },
        yAxis: {
          type: 'value'
        },
        series: [
          {
            data: this.workData,
            type: 'line'
          }
        ]
      })
    },
    setLifeOption () {
      this.lifeChart.setOption({
        color: ['#3398DB'],
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            // 坐标轴指示器，坐标轴触发有效
            type: 'shadow' // 默认为直线，可选为：'line' | 'shadow'
          }
        },
        grid: {
          left: '3%',
          right: '4%',
          bottom: '3%',
          containLabel: true
        },
        xAxis: [
          {
            type: 'category',
            data: ['周一', '周二', '周三', '周四', '周五', '周六', '周日'],
            axisTick: {
              alignWithLabel: true
            }
          }
        ],
        yAxis: [
          {
            type: 'value'
          }
        ],
        series: [
          {
            name: '直接访问',
            type: 'bar',
            barWidth: '60%',
            data: this.lifeData
          }
        ]
      })
    }
  },
  created () {
    setInterval(() => {
      // 一秒钟调用一次数据更新
      this.updateData()
    }, 1000)
  },
  mounted () {
    // 图表的初始化
    this.workChart = echarts.init(this.$refs.workChart)
    this.lifeChart = echarts.init(this.$refs.lifeChart)
    this.setWorkOption()
    this.setLifeOption()
  }
}
</script>

<style scoped>
.echarts {
  width: 600px;
  height: 400px;
}
</style>
