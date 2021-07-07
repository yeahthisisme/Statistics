<template>
  <div class="chart">
    <div class="align-center">
      <div class="left">数据分析</div>
      <div style="width: 50px"></div>
      <el-button size="small" plain>所有用户</el-button>
      <el-button size="small" plain>零售商</el-button>
      <el-button size="small" plain>其他用户</el-button>
    </div>
    <div class="flex">
      <div class="bottom" id="echart" ref="mychart"></div>
    </div>
  </div>
</template>

//
// <script src="./echarts.min.js"></script>
<script>

module.exports = {
  // data() {
  //   return {
  //     option: {
  //       xAxis: {
  //         type: "category",
  //         data: ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"],
  //       },
  //       yAxis: {
  //         type: "value",
  //       },
  //       series: [
  //         {
  //           data: [150, 230, 224, 218, 135, 147, 260],
  //           type: "line",
  //         },
  //       ],
  //     },
  //   }
  // },
  // mounted() {
  //   console.log(echarts)
  //   this.setEchart()
  // },
  // methods: {
  //   setEchart() {
  //     let dom = this.$refs.mychart
  //     this.myChart = echarts.init(dom)
  //     this.myChart.setOption(this.option)
  //   },
  //   chartChange() {
  //     this.myChart.setOption(this.opt)
  //     if (this.typePick == "百分比") {
  //       this.myChart.setOption(this.percent)
  //     }
  //     if (this.typePick == "数值") {
  //       this.myChart.setOption(this.numeric)
  //     }
  //   },
  // },
  data() {
    return {
      option: {
        xAxis: {
          type: "category",
          data: ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"],
        },
        yAxis: {
          type: "value",
        },
        series: [
          {
            data: [150, 230, 224, 218, 135, 147, 260],
            type: "line",
          },
        ],
      },
      typePick: "数值",
      typeList: [
        {
          name: "数值",
        },
        {
          name: "百分比",
        },
      ],
      pagePick: 0,
      // myChart实例
      myChart: {},
      percent: {
        label: {
          normal: {
            show: true,
            position: "inside",
            formatter: "{c}%",
          },
        },
      },
    }
  },
  mounted() {
    this.setEchart()
  },
  updated() {
    if (!this.myChart) {
      this.setEchart()
    }
    this.chartChange()
  },
  computed: {
    origin() {
      return this.data
    },
    opt() {
      let that = this
      let obj = {
        color: ["#606c94"],
        tooltip: {},
        toolbox: {
          show: true,
          feature: {
            saveAsImage: { show: true },
          },
        },
        label: {
          normal: {
            show: true,
            position: "inside",
            formatter: "{c}",
          },
          emphasis: {
            show: true,
          },
        },
        xAxis: {
          type: "value",
        },
        yAxis: {
          data: that.origin[that.type][that.pagePick].key,
          axisLabel: {
            interval: 0,
            rotate: -30,
          },
        },
      }
      return obj
    },
  },
  methods: {
    setEchart() {
      let dom = this.$refs.mychart
      this.myChart = echarts.init(dom)
      this.myChart.setOption(this.option)
    },
    chartChange() {
      this.myChart.setOption(this.opt)
      if (this.typePick == "百分比") {
        this.myChart.setOption(this.percent)
      }
      // if (this.typePick == "数值") {
      //   this.myChart.setOption(this.numeric)
      // }
    },
  },
}
</script>

<style>
.flex {
  display: flex;
}
.align-center {
  display: flex;
  align-items: center;
}
.chart {
  padding: 50px;
}
.left {
  width: 80px;
  font-weight: bold;
  position: relative;
  float: left;
}
.left::before {
  width: 4px;
  height: 20px;
  content: "";
  background: #169bd5;
  position: absolute;
  left: -10px;
}
</style>
