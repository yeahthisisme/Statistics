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
    <div v-show="activeIndex == 1">
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
    <div class="table-wrapper" v-show="activeIndex == 2">
      <el-row :gutter="10" type="flex" justify="end" align="middle">
        <el-col :span="7">
          用户身份：
          <el-select
            v-model="selectValue"
            size="small"
            filterable
            default-first-option
            placeholder="请选择"
          >
            <el-option
              v-for="item in identities"
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
          <el-table-column align="center" width="60">
            <template slot-scope="scope">
              <el-avatar :size="50" :src="scope.row.avatar"></el-avatar>
            </template>
          </el-table-column>
          <el-table-column
            label="用户名称"
            align="center"
            prop="userName"
          ></el-table-column>

          <el-table-column label="身份" width="100px" align="center">
            <template slot-scope="scope">
              <el-tag
                size="medium"
                :type="
                  scope.row.identity == 1
                    ? 'primary'
                    : scope.row.identity == 2
                    ? 'warning'
                    : scope.row.identity == 3
                    ? 'success'
                    : 'info'
                "
                >{{ formatType(scope.row.identity) }}</el-tag
              >
            </template>
          </el-table-column>
          <el-table-column label="手机号" align="center" width="140">
            <template slot-scope="scope"
              ><i class="el-icon-phone"></i>{{ scope.row.telephone }}</template
            >
          </el-table-column>

          <el-table-column
            label="店铺"
            align="center"
            prop="shopName"
            :show-overflow-tooltip="true"
          ></el-table-column>
          <el-table-column
            label="最近浏览"
            align="center"
            prop="latest"
            width="180"
          >
            <!-- <template slot-scope="scope">
            <span>{{ parseTime(scope.row.operTime) }}</span>
          </template> -->
          </el-table-column>
          <el-table-column
            label="浏览次数（PV）"
            align="center"
            prop="pv"
            sortable
            width="180"
          ></el-table-column>
          <el-table-column
            label="分享量"
            align="center"
            prop="share"
          ></el-table-column>
          <el-table-column label="查看入口" align="center">
            <template slot-scope="scope">
              <div
                class="center"
                :style="{ color: formatIsFrom(scope.row.isFrom).color }"
              >
                <i
                  style="font-size: 20px"
                  :class="formatIsFrom(scope.row.isFrom).icon"
                ></i>
                <span
                  >&nbsp;&nbsp;{{ formatIsFrom(scope.row.isFrom).method }}</span
                >
              </div>
            </template>
          </el-table-column>
        </el-table>
      </div>

      <div class="center" style="margin-top: 50px">
        <el-pagination
          :current-page.sync="currentPage"
          :page-size.sync="pageSize"
          :total="total"
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
              var date = new Date(value);
              var texts = [
                date.getMonth() + 1,
                date.getDate() < 10 ? "0" + date.getDate() : date.getDate(),
              ];
              return texts.join("-");
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
              var date = new Date(value);
              var texts = [
                date.getMonth() + 1,
                date.getDate() < 10 ? "0" + date.getDate() : date.getDate(),
              ];
              return texts.join("-");
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

      // ----------------------------查看产品的用户
      // 表格数据
      list: [
        {
          avatar:
            "https://youlala.oss-accelerate.aliyuncs.com/lihaoaapp/2021/07/02/eadb78e3-5911-477e-9994-2413c4e15f9ajpg?t=1625208263431",
          userName: "小明",
          identity: 1,
          telephone: "18652456453",
          shopName: "燕归来",
          latest: "2021-05-23 22:23:43",
          pv: 1134,
          share: 322,
          isFrom: "3",
        },
        {
          avatar:
            "https://youlala.oss-accelerate.aliyuncs.com/lihaoaapp/2021/05/18/913dae86-b084-48d5-b716-eed9bab48b10jpg?t=1621326254358",
          userName: "李娜",
          identity: 3,
          telephone: "13564536666",
          shopName: "黔贵之旅",
          latest: "2021-04-13 12:23:43",
          pv: 555,
          share: 142,
          isFrom: "4",
        },
      ],
      // 遮罩层
      loading: false,
      // 总条数
      total: 0,
      //用户身份
      identities: [
        {
          label: "所有",
          value: 0,
        },
        {
          label: "零售商",
          value: 1,
        },
      ],
      //当前选定值为 “所有”
      selectValue: 0,
      //当前页
      currentPage: 1,
      // 分页页面大小
      pageSize: 10,
      //总页数
      total: 1,
    };
  },
  mounted() {
    const end = new Date();
    const start = new Date();
    start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
    this.value1 = this.value2 = [start, end];

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
    ];
    //1.请求图表数据
    //2.画图
    this.getChartList();

    this.getAllTableList();
  },
  updated() {
    this.drawChart();
  },
  methods: {
    drawChart(type) {
      if (!this.userChart || !this.newUserChart) {
        this.setEchart();
      } else {
        this.changeChart(type);
      }
    },
    setEchart() {
      let user = this.$refs.mychart1;
      this.userChart = echarts.init(user);
      this.userChart.setOption(this.option1);

      let newUser = this.$refs.mychart2;
      this.newUserChart = echarts.init(newUser);
      this.newUserChart.setOption(this.option2);
    },
    changeChart(isNew) {
      //isNew为undefined 两个图都更新
      if (isNew != "newUser") {
        this.userChart.setOption(this.option1);
      }
      if (isNew != "user") {
        this.newUserChart.setOption(this.option2);
      }
    },
    radioChange(e) {
      console.log(e);
      console.log(this.submitParam.type);
      this.getChartList();
    },
    dateChange(isNew) {
      console.log(isNew);
      console.log(this.value1, this.value2);
      if (isNew == "user") {
        //this.value1是数组储存startTime,endTime; moment(endTime).format("yyyy-MM-dd")
        this.submitParam.user[0].startTime = moment(this.value1[0]).format(
          "yyyy-MM-dd"
        );
        this.submitParam.user[0].endTime = moment(this.value1[1]).format(
          "yyyy-MM-dd"
        );
      } else {
        // isNew=="newUser"
        //this.value1是数组储存startTime,endTime; moment(endTime).format("yyyy-MM-dd")
        this.submitParam.user[1].startTime = moment(this.value2[0]).format(
          "yyyy-MM-dd"
        );
        this.submitParam.user[1].endTime = moment(this.value2[1]).format(
          "yyyy-MM-dd"
        );
      }
      this.getChartList(isNew);
    },
    getChartList(isNew) {
      // this.submitParam
      //axios.get(this.submitParam).then(res=>{})
      // 返回数据更改option1
      //再画一遍图
      this.drawChart(isNew);
    },
    getAllTableList() {
      //请求一览表数据
      //修改tableList
    },
    indexMethods(index) {
      // currentpage当前页码，this.list.length总条数，index索引值
      if (this.list.length < 10) {
        return this.list.length - index;
      } else {
        return this.list.length - (this.queryParams.pageNum - 1) * 10 - index;
      }
    },
    handleSelect(key, keyPath) {
      console.log(key, keyPath);
      this.activeIndex = key;
      if (key == 2) {
        this.getList();
      }
    },
    /** 查询登录日志 */
    getList() {
      this.loading = true;
      this.total = this.list.length;
      this.loading = false;
    },
    formatType(value) {
      switch (value) {
        case 1:
        case "1":
          value = "员工";
          break;
        case 2:
        case "2":
          value = "零售商";
          break;
        case 3:
        case "3":
          value = "商家零售";
          break;
        case 4:
        case "4":
          value = "其他用户";
          break;
      }
      return value;
    },
    formatIsFrom(value) {
      switch (value) {
        case 1:
        case "1":
          value = {
            method: "产品海报",
            color: "#E6A23C",
            icon: "el-icon-picture",
          };
          break;
        case 2:
        case "2":
          value = {
            method: "微信卡片",
            color: "#67C23A",
            icon: "el-icon-copy-document",
          };
          break;
        case 3:
        case "3":
          value = {
            method: "旅拍",
            color: "#409EFF",
            icon: "el-icon-camera-solid",
          };
          break;
        case 4:
        case "4":
          value = {
            method: "自主浏览",
            color: "#F56C6C",
            icon: "el-icon-mobile-phone",
          };
          break;
      }
      return value;
    },
    /** 导出按钮操作 */
    handleExport() {
      const queryParams = this.queryParams;
      this.$confirm("是否确认导出所有数据项?", "警告", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      }).then(function () {
        //1.调数据接口返回response.msg
        //2.
        this.download(response.msg);
      });
    },
    download(fileName) {
      window.location.href =
        baseURL +
        "/common/download?fileName=" +
        encodeURI(fileName) +
        "&delete=" +
        true;
    },
    //页面改变
    handleCurrentChange(e) {
      console.log(e);
    },
  },
};
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
