<template>
  <div class="app-container">
    <el-form
      :model="queryParams"
      ref="queryForm"
      :inline="true"
      v-show="showSearch"
    >
      <el-form-item label="首次分享时间：" class="label">
        <el-date-picker
          v-model="value2"
          type="daterange"
          size="small"
          align="right"
          unlink-panels
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          :picker-options="pickerOptions"
          style="width: 240px"
        >
        </el-date-picker>
      </el-form-item>
      <el-form-item label="分享人：" class="label">
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
      </el-form-item>
      <el-form-item label="产品分类：" prop="productType" class="label">
        <el-select
          v-model="queryParams.productType"
          placeholder="请选择"
          clearable
          size="small"
          style="width: 120px"
        >
          <el-option
            v-for="item in typeOptions"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          />
        </el-select>
      </el-form-item>
      <el-form-item label="产品名称：" prop="title" class="label">
        <el-input
          v-model="queryParams.title"
          placeholder="请输入产品名称"
          clearable
          size="small"
          @keyup.enter.native="handleQuery"
        />
      </el-form-item>

      <el-form-item>
        <el-button
          type="primary"
          icon="el-icon-search"
          size="mini"
          @click="handleQuery"
          >搜索</el-button
        >
        <el-button icon="el-icon-refresh" size="mini" @click="resetQuery"
          >重置</el-button
        >
      </el-form-item>
    </el-form>

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

      <el-table-column label="产品分类" width="100px" align="center">
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

    <div class="center">
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

    <!-- 操作日志详细 -->
    <el-dialog title="访客" :visible.sync="open" width="700px">
      <el-table v-loading="loading" :data="list">
        <el-table-column
          label="序号"
          align="center"
          type="index"
          :index="indexMethods"
        ></el-table-column>
        <el-table-column
          label="用户名称"
          align="center"
          prop="people"
        ></el-table-column>
        <el-table-column
          label="手机号"
          align="center"
          prop="telephone"
        ></el-table-column>
        <el-table-column label="身份" width="100px" align="center">
          <template slot-scope="scope">
            <span>{{ formatType(scope.row.peopleType) }}</span>
          </template>
        </el-table-column>
        <el-table-column
          label="浏览量（PV）"
          align="center"
          prop="pv"
          width="130"
        ></el-table-column>
        <el-table-column
          label="最近浏览"
          align="center"
          prop="shareTime"
          width="180"
        >
        </el-table-column>
      </el-table>
      <div slot="footer" class="dialog-footer">
        <el-pagination
          :current-page.sync="currentPage"
          :page-size.sync="pageSize"
          :layout="layout"
          :page-sizes="pageSizes"
          :total="total"
          @current-change="handleCurrentChange"
        />
      </div>
    </el-dialog>
  </div>
</template>

<script>
module.exports = {
  name: "statistics",
  data() {
    return {
      // 遮罩层
      loading: true,
      // 非多个禁用
      multiple: true,
      // 显示搜索条件
      showSearch: true,
      // 总条数
      total: 0,
      // 表格数据
      list: [
        {
          shareTime: "2021-05-23 22:23:43",
          people: "小明",
          img: "https://youlala.oss-accelerate.aliyuncs.com/lihaoaapp/2021/07/02/eadb78e3-5911-477e-9994-2413c4e15f9ajpg?t=1625208263431",
          title: "桂林3天2晚桂林3天2晚桂林3天2晚",
          productType: 3,
          peopleType: 3,
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
          peopleType: 3,
          totalUv: 142,
          uv: 34,
          pv: 555,
        },
      ],
      pickerOptions: {
        shortcuts: [
          {
            text: "今天",
            onClick(picker) {
              const end = new Date()
              const start = new Date()
              picker.$emit("pick", [start, end])
            },
          },
          {
            text: "最近七天",
            onClick(picker) {
              const end = new Date()
              const start = new Date()
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 7)
              picker.$emit("pick", [start, end])
            },
          },
          {
            text: "最近一个月",
            onClick(picker) {
              const end = new Date()
              const start = new Date()
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 30)
              picker.$emit("pick", [start, end])
            },
          },
        ],
      },
      value2: "",
      options: [
        {
          value: "HTML",
          label: "HTML",
        },
        {
          value: "CSS",
          label: "CSS",
        },
        {
          value: "JavaScript",
          label: "JavaScript",
        },
      ],
      typeOptions: [
        {
          label: "单订房",
          value: 1,
        },
        {
          label: "散拼团",
          value: 2,
        },
        {
          label: "小包团",
          value: 3,
        },
        {
          label: "自由行",
          value: 4,
        },
      ],
      // 是否显示弹出层
      open: false,
      // 日期范围
      dateRange: [],
      // 表单参数
      form: {},
      // 查询参数
      queryParams: {
        pageNum: 1,
        pageSize: 10,
        title: undefined,
        operName: undefined,
        peopleType: undefined,
      },
    }
  },
  created() {
    this.getList()
  },
  methods: {
    formatType(){

    },
    indexMethods(index) {
      // currentpage当前页码，this.list.length总条数，index索引值
      if (this.list.length < 10) {
        return this.list.length - index
      } else {
        return this.list.length - (this.queryParams.pageNum - 1) * 10 - index
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
    /** 搜索按钮操作 */
    handleQuery() {
      this.queryParams.pageNum = 1
      this.getList()
    },
    /** 重置按钮操作 */
    resetQuery() {
      this.dateRange = []
      this.resetForm("queryForm")
      this.handleQuery()
    },
    // 多选框选中数据
    handleSelectionChange(selection) {
      this.ids = selection.map((item) => item.operId)
      this.multiple = !selection.length
    },
    /** 详细按钮操作 */
    handleView(row) {
      this.open = true
      this.form = row
    },
  },
}
</script>

<style>
.label {
  font-weight: bold;
}
.center {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 50px;
}
</style>
