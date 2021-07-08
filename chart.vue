<template>
  <div class="chart-container">
    <div class="align-center">
      <div class="left">数据分析</div>
      <div style="width: 50px"></div>
      <el-radio-group v-model="submitParam.type" @change="radioChange">
        <el-radio-button size="small" label="所有用户"></el-radio-button>
        <el-radio-button size="small" label="零售商"></el-radio-button>
        <el-radio-button size="small" label="其他用户"></el-radio-button>
      </el-radio-group>
    </div>
    <div class="space-around wrapper">
      <div>
        <div class="chart" id="echart1" ref="mychart1"></div>
        <div class="center">
          <el-date-picker
            v-model="value1"
            type="daterange"
            size="small"
            style="width: 240px"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            :editable="false"
            @change="dateChange('user')"
          >
          </el-date-picker>
        </div>
      </div>
      <div>
        <div class="chart" id="echart2" ref="mychart2"></div>
        <div class="center">
          <el-date-picker
            v-model="value2"
            type="daterange"
            size="small"
            style="width: 240px"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            :editable="false"
            @change="dateChange('newUser')"
          >
          </el-date-picker>
        </div>
      </div>
    </div>
    <div class="table-container">
      <div class="left">数据一览</div>
      <div class="table">
        <div class="table-tr">
          <div class="table-td">
            <div class="sub-table">
              <div class="sub-table-tr">
                <div class="table-th" style="width: 10%"></div>
                <div class="table-th" style="width: 30%">访客量(UV)</div>
                <div class="table-th" style="width: 30%">浏览量(PV)</div>
                <div class="table-th" style="width: 30%">新增用户</div>
              </div>
            </div>
          </div>
        </div>
        <div class="table-tr">
          <div class="table-td">
            <div class="sub-table">
              <div class="sub-table-tr">
                <div class="table-th" style="width: 10%"></div>
                <div class="table-th" style="width: 10%">所有用户</div>
                <div class="table-th" style="width: 10%">零售商</div>
                <div class="table-th" style="width: 10%">其他用户</div>
                <div class="table-th" style="width: 10%">所有用户</div>
                <div class="table-th" style="width: 10%">零售商</div>
                <div class="table-th" style="width: 10%">其他用户</div>
                <div class="table-th" style="width: 10%">所有用户</div>
                <div class="table-th" style="width: 10%">零售商</div>
                <div class="table-th" style="width: 10%">其他用户</div>
              </div>
            </div>
          </div>
        </div>
        <div class="table-tr" v-for="(item, i) in tableList" :key="i">
          <div class="table-td">
            <div class="sub-table">
              <div class="sub-table-tr">
                <div
                  :class="{ 'table-th': index == 0, 'sub-table-td': index > 0 }"
                  style="width: 10%"
                  v-for="(cell, index) in item"
                  :key="index"
                >
                  {{ cell }}
                </div>
              </div>
            </div>
          </div>
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
            { name: "浏览量（PV）", textStyle: { color: "#05a3d7" } },
            { name: "访客量（UV）", textStyle: { color: "#fb5531" } },
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
            axisLine: {
              symbol: ["none", "arrow"],
              symbolOffset: [10, 10],
            },
          },
          {
            type: "value",
            axisLine: {
              symbol: ["none", "arrow"],
              symbolOffset: [10, 10],
            },
          },
        ],
        series: [
          {
            name: "浏览量（PV）",
            type: "line",
            itemStyle: { normal: { color: "#05a3d7", label: { show: true } } },
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
            itemStyle: { normal: { color: "#fb5531", label: { show: true } } },
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
        title: {
          text: "小店新增用户",
          fontWeight: "normal",
          left: "center",
        },
        legend: {
          right: 20,
          orient: "vertical",
          data: [{ name: "新增用户（UV）", textStyle: { color: "#fb5531" } }],
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
            axisLine: {
              symbol: ["none", "arrow"],
              symbolOffset: [10, 10],
            },
          },
        ],
        series: [
          {
            name: "新增用户（UV）",
            type: "line",
            itemStyle: { normal: { color: "#fb5531", label: { show: true } } },
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
      // myChart实例
      userChart: {},
      newUserChart: {},
      tableList: [
        ["今天", 10, 23, 45, 23, 432, 23, 12, 34, 675],
        ["昨天", 10, 23, 45, 23, 432, 23, 12, 34, 675],
        ["本周", 10, 23, 45, 23, 432, 23, 12, 34, 675],
        ["本月", 10, 23, 45, 23, 432, 23, 12, 34, 675],
        ["总计", 10, 23, 45, 23, 432, 23, 12, 34, 675],
        ["自定义时间", 10, 23, 45, 23, 432, 23, 12, 34, 675],
      ],
      submitParam: {
        type: "所有用户",
        date1: "",
        date2: "",
      },
    }
  },
  mounted() {
    this.setEchart()

    this.getAllTableList()
  },
  updated() {
    if (!this.userChart || !this.newUserChart) {
      this.setEchart()
    }
    this.changeChart()
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
    changeChart(type) {
      if (type != "newUser") {
        this.userChart.setOption(this.option1)
      }
      if (type != "user") {
        this.newUserChart.setOption(this.option2)
      }
    },
    radioChange(e) {
      console.log(e)
      console.log(this.submitParam.type)
      //1、请求拿数据
      //2、修改option
      //3、重新绘图
      // this.changeChart()
    },
    dateChange(e) {
      if (e == "user") {
        //请求
        //this.value1是数组储存startTime,endTime; moment(endTime).format("yyyy-MM-dd")
        // var endTime = moment(this.value1[1]).format("yyyy-MM-dd")
        //axios.get(date:this.value1).then(res=>{})
        // 返回数据更改option1
        //再画一遍图
        // this.changeChart(e)
      } else {
        // e=="newUser"
      }
      console.log(e)
      console.log(this.value1, this.value2)
    },
    getAllTableList() {
      //请求一览表数据
      //修改tableList
    },
  },
}
</script>

<style>
.align-center {
  display: flex;
  align-items: center;
}
.center {
  display: flex;
  justify-content: center;
  align-items: center;
}
.chart-container {
  padding: 50px;
}
.left {
  width: 80px;
  font-weight: bold;
  position: relative;
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
.space-around {
  display: flex;
  justify-content: space-around;
  width: 100%;
}
.chart {
  width: 500px;
  height: 500px;
}
.table-container {
  margin-top: 100px;
  margin-bottom: 100px;
}
.table {
  display: table;
  width: 980px;
  border-collapse: collapse;
  margin-top: 40px;
  margin-left: 60px;
}
.table * {
  margin: 0 auto;
  padding: 0;
  font-size: 14px;
  font-family: Arial, 宋体, Helvetica, sans-serif;
}
.table-tr {
  display: table-row;
}
.table-th {
  display: table-cell;
  font-weight: bold;
  height: 34px;
  border: 1px solid gray;
  text-align: center;
  vertical-align: middle;
  background-color: #f2f2f2;
}
.table-td {
  display: table-cell;
  height: 34px;
}

.sub-table {
  width: 100%;
  height: 100%;
  display: table;
}
.sub-table-tr {
  display: table-row;
  height: 100%;
}
.sub-table-td {
  display: table-cell;
  height: 100%;
  border: 1px solid gray;
  text-align: center;
  vertical-align: middle;
}
</style>
