<template>
  <div class="s-container">
    <el-menu
      :default-active="activeIndex"
      class="el-menu-demo"
      mode="horizontal"
      @select="handleSelect"
    >
      <el-menu-item index="1">数据分析</el-menu-item>
      <el-menu-item index="2">查看产品的用户</el-menu-item>
    </el-menu>
    <div v-show="false">
      <div class="top">
        <div class="bg">
          <div
            class="item column-center"
            v-for="item in allList"
            :key="item.id"
          >
            <span class="span1">{{ item.num }}</span>
            <span class="span2">{{ item.title }}</span>
          </div>
        </div>
      </div>
      <div class="chart-container">
        <div class="align-center">
          <div class="left">数据分析</div>
          <div style="width: 50px"></div>
          <el-radio-group v-model="submitParam.type" @change="radioChange">
            <el-radio-button size="small" label="所有用户"></el-radio-button>
            <el-radio-button size="small" label="零售商"></el-radio-button>
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
      </div>
    </div>
    <div class="table-wrapper">
      <el-row :gutter="10" type="flex" justify="end" align="middle">
        <el-col :span="7">
          用户身份：
          <el-select
            v-model="value"
            multiple
            size="small"
            filterable
            allow-create
            default-first-option
            placeholder="请选择"
          >
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
        </el-col>
        <el-col :span="2">
          <el-button
            type="primary"
            plain
            icon="el-icon-download"
            size="small"
            @click="handleExport"
            >导出</el-button
          >
        </el-col>
      </el-row>
      <div style="margin-top: 20px">
        <el-table v-loading="loading" :data="list">
          <el-table-column
            label="序号"
            align="center"
            type="index"
            :index="indexMethods"
          ></el-table-column>
          <el-table-column
            label="首次分享"
            align="center"
            prop="shareTime"
            width="180"
          >
            <!-- <template slot-scope="scope">
            <span>{{ parseTime(scope.row.operTime) }}</span>
          </template> -->
          </el-table-column>
          <el-table-column
            label="分享人"
            align="center"
            prop="people"
          ></el-table-column>
          <el-table-column align="center" width="90">
            <template slot-scope="scope">
              <img
                :src="scope.row.img"
                :alt="scope.row.title"
                style="width: 80px; height: 80px"
              />
            </template>
          </el-table-column>
          <el-table-column
            label="产品名称"
            align="center"
            prop="title"
            :show-overflow-tooltip="true"
          ></el-table-column>

          <el-table-column label="身份" width="100px" align="center">
            <template slot-scope="scope">
              <span>{{ formatType(scope.row.productType) }}</span>
            </template>
          </el-table-column>

          <el-table-column
            label="总访客量（UV）"
            width="100"
            sortable
            align="center"
          >
            <template slot-scope="scope">
              <el-button
                size="mini"
                type="text"
                icon="el-icon-view"
                @click="handleView(scope.row, scope.index)"
                >{{ scope.row.totalUv }}</el-button
              >
            </template>
          </el-table-column>
          <el-table-column label="零售商（UV）" width="80" align="center">
            <template slot-scope="scope">
              <el-button
                size="mini"
                type="text"
                icon="el-icon-view"
                @click="handleView(scope.row, scope.index)"
                >{{ scope.row.uv }}</el-button
              >
            </template>
          </el-table-column>
          <el-table-column width="100"></el-table-column>
          <el-table-column
            label="总浏览量（PV）"
            align="center"
            prop="pv"
            width="130"
          ></el-table-column>
        </el-table>
      </div>

      <div class="center" style="margin-top: 50px">
        <el-pagination
          :background="true"
          :current-page.sync="currentPage"
          :page-size.sync="pageSize"
          :layout="layout"
          :page-sizes="pageSizes"
          :total="total"
          v-bind="$attrs"
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
        />
      </div>
    </div>
  </div>
</template>

<script>
module.exports = {
  data() {
    return {
      activeIndex: "1",
      allList: [
        {
          id: 1,
          title: "总访客量 （UV）",
          num: 1523,
        },
        {
          id: 2,
          title: "零售商（UV）",
          num: 267,
        },
        {
          id: 3,
          title: "总浏览量（PV）",
          num: 2524,
        },
        {
          id: 4,
          title: "分享量",
          num: 531,
        },
      ],
      value1: "",
      value2: "",
      option1: {
        title: {
          text: "查看产品的用户",
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
          text: "新增查看用户",
          fontWeight: "normal",
          left: "center",
        },
        legend: {
          right: 20,
          orient: "vertical",
          data: [
            { name: "新增查看用户（UV）", textStyle: { color: "#05a3d7" } },
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
        ],
        series: [
          {
            name: "新增查看用户（UV）",
            type: "line",
            itemStyle: { normal: { color: "#05a3d7", label: { show: true } } },
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
      userChart: null,
      newUserChart: null,
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
        user: [
          {
            isNew: "user",
            startTime: "",
            endTime: "",
          },
          {
            isNew: "newUser",
            startTime: "",
            endTime: "",
          },
        ],
      },

      // 表格数据
      list: [
        {
          shareTime: "2021-05-23 22:23:43",
          people: "小明",
          img: "https://youlala.oss-accelerate.aliyuncs.com/lihaoaapp/2021/07/02/eadb78e3-5911-477e-9994-2413c4e15f9ajpg?t=1625208263431",
          title: "桂林3天2晚桂林3天2晚桂林3天2晚",
          productType: 3,
          totalUv: 322,
          uv: 234,
          pv: 1134,
        },
        {
          shareTime: "2021-04-13 12:23:43",
          people: "李娜",
          img: "https://youlala.oss-accelerate.aliyuncs.com/lihaoaapp/2021/05/18/913dae86-b084-48d5-b716-eed9bab48b10jpg?t=1621326254358",
          title: "桂林3天2晚",
          productType: 3,
          totalUv: 142,
          uv: 34,
          pv: 555,
        },
      ],
      // 遮罩层
      // loading: true,
      // 非多个禁用
      multiple: true,
      // 显示搜索条件
      showSearch: true,
      // 总条数
      total: 0,
    }
  },
  mounted() {
    const end = new Date()
    const start = new Date()
    start.setTime(start.getTime() - 3600 * 1000 * 24 * 7)
    this.value1 = this.value2 = [start, end]

    this.submitParam.user = [
      {
        isNew: "user",
        startTime: start,
        endTime: end,
      },
      {
        isNew: "newUser",
        startTime: start,
        endTime: end,
      },
    ]
    //1.请求图表数据
    //2.画图
    this.getChartList()

    this.getAllTableList()
  },
  updated() {
    this.drawChart()
  },
  methods: {
    drawChart(type) {
      if (!this.userChart || !this.newUserChart) {
        this.setEchart()
      } else {
        this.changeChart(type)
      }
    },
    setEchart() {
      let user = this.$refs.mychart1
      this.userChart = echarts.init(user)
      this.userChart.setOption(this.option1)

      let newUser = this.$refs.mychart2
      this.newUserChart = echarts.init(newUser)
      this.newUserChart.setOption(this.option2)
    },
    changeChart(isNew) {
      //isNew为undefined 两个图都更新
      if (isNew != "newUser") {
        this.userChart.setOption(this.option1)
      }
      if (isNew != "user") {
        this.newUserChart.setOption(this.option2)
      }
    },
    radioChange(e) {
      console.log(e)
      console.log(this.submitParam.type)
      this.getChartList()
    },
    dateChange(isNew) {
      console.log(isNew)
      console.log(this.value1, this.value2)
      if (isNew == "user") {
        //this.value1是数组储存startTime,endTime; moment(endTime).format("yyyy-MM-dd")
        this.submitParam.user[0].startTime = moment(this.value1[0]).format(
          "yyyy-MM-dd"
        )
        this.submitParam.user[0].endTime = moment(this.value1[1]).format(
          "yyyy-MM-dd"
        )
      } else {
        // isNew=="newUser"
        //this.value1是数组储存startTime,endTime; moment(endTime).format("yyyy-MM-dd")
        this.submitParam.user[1].startTime = moment(this.value2[0]).format(
          "yyyy-MM-dd"
        )
        this.submitParam.user[1].endTime = moment(this.value2[1]).format(
          "yyyy-MM-dd"
        )
      }
      this.getChartList(isNew)
    },
    getChartList(isNew) {
      // this.submitParam
      //axios.get(this.submitParam).then(res=>{})
      // 返回数据更改option1
      //再画一遍图
      this.drawChart(isNew)
    },
    getAllTableList() {
      //请求一览表数据
      //修改tableList
    },
    indexMethods(index) {
      // currentpage当前页码，this.list.length总条数，index索引值
      if (this.list.length < 10) {
        return this.list.length - index
      } else {
        return this.list.length - (this.queryParams.pageNum - 1) * 10 - index
      }
    },
    handleSelect(key, keyPath) {
      console.log(key, keyPath)
      this.activeIndex = key
      if (key == 2) {
        this.getList()
      }
    },
    /** 查询登录日志 */
    getList() {
      this.loading = true
      this.total = this.list.length
      this.loading = false
    },
    formatType(value) {
      switch (value) {
        case 1 || "1":
          value = "单订房"
          break
        case 2 || "2":
          value = "散拼团"
          break
        case 3 || "3":
          value = "小包团"
          break
        case 4 || "4":
          value = "自由行"
          break
      }
      return value
    },
    /** 导出按钮操作 */
    handleExport() {
      const queryParams = this.queryParams
      this.$confirm("是否确认导出所有数据项?", "警告", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(function () {
          //调数据接口
        })
        .then((response) => {
          this.download(response.msg)
        })
    },
    download(fileName) {
      window.location.href = baseURL + "/common/download?fileName=" + encodeURI(fileName) + "&delete=" + true;
    }
  },
}
</script>

<style>
.s-container {
  padding: 50px;
}
.s-container .top {
  padding: 60px 100px;
}
.s-container .top .bg {
  display: flex;
  justify-content: space-around;
  width: 700px;
  height: 100px;
  padding: 26px;
  box-sizing: border-box;
  background: #f2f2f2;
  border-radius: 10px;
}
.top .bg .item {
  width: 180px;
  text-align: center;
}
.top .bg .item + .item {
  border-left: 2px solid #afafaf;
}
.top .bg .item .span1 {
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 3px;
}
.top .bg .item .span2 {
  font-size: 12px;
  color: gray;
}
.align-center {
  display: flex;
  align-items: center;
}
.column-center {
  display: flex;
  flex-direction: column;
  justify-content: center;
}
.center {
  display: flex;
  justify-content: center;
  align-items: center;
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

.table-wrapper {
  padding: 20px;
}
[class*="el-col-"] {
  float: left;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  text-align: right;
}
</style>
