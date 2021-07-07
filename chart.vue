<template>
  <div class="chart-container">
    <div class="align-center">
      <div class="left">数据分析</div>
      <div style="width: 50px"></div>
      <el-button size="small" plain>所有用户</el-button>
      <el-button size="small" plain>零售商</el-button>
      <el-button size="small" plain>其他用户</el-button>
    </div>
    <div class="flex wrapper">
      <div>
        <div class="chart" id="echart1" ref="mychart1"></div>
        <div>
          <el-date-picker
            v-model="value1"
            type="daterange"
            size="small"
            style="width: 240px"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            change="chartChange(1)"
          >
          </el-date-picker>
        </div>
      </div>
      <div>
        <div class="chart" id="echart2" ref="mychart2"></div>
        <div>
          <el-date-picker
            v-model="value2"
            type="daterange"
            size="small"
            style="width: 240px"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            change="chartChange(2)"
          >
          </el-date-picker>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
module.exports = {
  data() {
    return {
      value1: "",
      value2: "",
      option1: {
        title: {
          text: "小店用户",
          fontWeight: "normal",
          left: "center",
        },
        legend: {
          right: 20,
          orient: "vertical",
          data: [
            { name: "浏览量（PV）", textStyle: { color: "#86cde5" } },
            { name: "访客量（UV）", textStyle: { color: "#febb77" } },
          ],
        },
        tooltip: {
          trigger: "axis",
        },
        xAxis: {
          type: "category",
          axisTick: {
            alignWithLabel: true,
          },
          // data: [
          //   "2021-07-07",
          //   "2021-07-08",
          //   "2021-07-09",
          //   "2021-07-10",
          //   "2021-07-11",
          //   "2021-07-12",
          //   "2021-07-13",
          // ],
          axisLabel: {
            formatter: function (value, index) {
              // 格式化成月/日，只在第一个刻度显示年份
              var date = new Date(value)
              var texts = [
                date.getMonth() + 1,
                date.getDate() < 10 ? "0" + date.getDate() : date.getDate(),
              ]
              return texts.join("-")
            },
          },
        },
        yAxis: [
          {
            type: "value",
          },
          {
            type: "value",
          },
        ],
        series: [
          {
            name: "浏览量（PV）",
            type: "line",
            itemStyle: { normal: { color: "#86cde5", label: { show: true } } },
            data: [
              ["2020-10-3", 135],
              ["2020-10-4", 167],
              ["2020-10-5", 24],
              ["2020-10-6", 250],
              ["2020-10-7", 560],
              ["2020-10-8", 230],
              ["2020-10-9", 30],
              ["2020-10-10", 180],
            ],
          },
          {
            name: "访客量（UV）",
            type: "line",
            itemStyle: { normal: { color: "#febb77", label: { show: true } } },
            data: [
              ["2020-10-3", 240],
              ["2020-10-4", 110],
              ["2020-10-5", 40],
              ["2020-10-6", 150],
              ["2020-10-7", 330],
              ["2020-10-8", 130],
              ["2020-10-9", 80],
              ["2020-10-10", 180],
            ],
          },
        ],
      },
      option2: {
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
          name: "浏览量（PV）",
        },
        {
          name: "访客量（UV）",
        },
      ],
      pagePick: 0,
      // myChart实例
      userChart: {},
      newUserChart: {},
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
  methods: {
    setEchart() {
      let user = this.$refs.mychart1
      this.userChart = echarts.init(user)
      this.userChart.setOption(this.option1)

      let newUser = this.$refs.mychart2
      this.newUserChart = echarts.init(newUser)
      this.newUserChart.setOption(this.option2)
    },
    chartChange(type) {
      if (type != 2) {
        this.userChart.setOption(this.option1)
      }
      if (type != 1) {
        this.newUserChart.setOption(this.option2)
      }
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
.chart-container {
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
.wrapper {
  margin-top: 50px;
}
.chart {
  width: 500px;
  height: 500px;
}
</style>
