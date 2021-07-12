<template>
  <div class="Information">
    <div class="Information-title">
      <el-card>
        <el-form
          :inline="true"
          :model="formSearch"
          class="demo-form-inline"
          size="mini"
        >
          <el-form-item label="姓名">
            <el-input
              v-model="formSearch.trueName"
              placeholder="请输入姓名"
            ></el-input>
          </el-form-item>
          <el-form-item label="手机号">
            <el-input
              v-model="formSearch.phone"
              placeholder="请输入手机号"
            ></el-input>
          </el-form-item>
          <el-form-item>
            <el-button
              type="primary"
              @click="submitFormSearch"
              icon="el-icon-search"
              >查询</el-button
            >
            <el-button
              type="primary"
              @click="exportTable"
              icon="el-icon-download"
              >导出</el-button
            >
          </el-form-item>
        </el-form>
      </el-card>
    </div>
    <div class="Information-table">
      <el-table
        v-loading="loading"
        element-loading-text="拼命加载中"
        ref="multipleTable"
        :data="consultBillList"
        tooltip-effect="dark"
        style="width：100%"
        :border="true"
        size="mini"
        :height="tableConfig.height"
        @selection-change="handleSelectionChange"
      >
        <!-- <el-table-column label="序号">
          <template slot-scope="scope">
            {{ scope.row.id }}
          </template>
        </el-table-column> -->
        <el-table-column type="index" width="50" label="序号"> </el-table-column>
        <el-table-column label="账号">
          <template slot-scope="scope">
            {{ scope.row.username }}
          </template>
        </el-table-column>

        <el-table-column label="密码">
          <template slot-scope="scope">
            {{ scope.row.password }}
          </template>
        </el-table-column>

        <el-table-column label="姓名">
          <template slot-scope="scope">
            {{ scope.row.trueName }}
          </template>
        </el-table-column>

        <el-table-column label="手机号">
          <template slot-scope="scope">
            {{ scope.row.mobile }}
          </template>
        </el-table-column>

        <el-table-column label="区/县">
          <template slot-scope="scope">
            {{ scope.row.district }}
          </template>
        </el-table-column>

        <el-table-column label="街道/村镇">
          <template slot-scope="scope">
            {{ scope.row.street }}
          </template>
        </el-table-column>

        <el-table-column label="是否启用">
          <template slot-scope="scope">
            {{ scope.row.status === 1 ? "启用" : "禁用" }}
          </template>
        </el-table-column>

        <el-table-column label="操作" align="left" width="170" fixed="right">
          <template slot-scope="scope">
            <el-button
              size="mini"
              type="primary"
              @click="handleEdit(scope.$index, scope.row)"
              icon="el-icon-edit"
            ></el-button>
            <el-button
              size="mini"
              type="danger"
              @click="handleDelete(scope.$index, scope.row)"
              icon="el-icon-delete"
            ></el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>

    <!-- 分页条 -->
    <div class="block">
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="currentPage"
        :page-sizes="[20, 40, 80, 100]"
        :page-size="pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="pageTotal"
      >
      </el-pagination>
    </div>

    <el-dialog title="管理人员修改" :visible.sync="dialogFormVisible">
      <el-form :model="formEdit" label-width="100px">
        <el-form-item label="账号">
          <el-input v-model="formEdit.username" autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item label="密码">
          <el-input v-model="formEdit.password" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="姓名">
          <el-input v-model="formEdit.trueName" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="手机号">
          <el-input v-model="formEdit.mobile" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="区/县">
          <el-select
            v-model="formEdit.district"
            placeholder="请选择区/县"
            @change="currentSelectValue"
          >
            <el-option
              v-for="dtt in districts"
              :label="dtt.label"
              :key="dtt.value"
              :value="dtt.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="街道/村镇">
          <el-select v-model="formEdit.street" placeholder="请选择街道/村镇">
            <el-option
              v-for="street in streets"
              :label="street.label"
              :key="street.value"
              :value="street.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="是否启用">
          <el-switch
            v-model="formEdit.status"
            :active-value="1"
            :inactive-value="0"
          ></el-switch>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false" size="mini"
          >取 消</el-button
        >
        <el-button type="primary" @click="dialogFormConfirm" size="mini"
          >确 定</el-button
        >
      </div>
    </el-dialog>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      consultBillList: [],
      multipleSelection: [],
      currentPage: 1, // 当前页
      pageTotal: null,
      pageSize: 100,
      loading: true,
      tableConfig: {
        height: window.innerHeight - 300,
        isLoading: true,
      },
      formSearch: {
        trueName: "",
        phone: "",
      },
      dialogFormVisible: false,
      formEdit: {
        id: "",
        username: "",
        password: "",
        trueName: "",
        mobile: "",
        district: "123",
        street: "",
        status: "",
      },
      districts: [], // 区/县
      streets: [], // 街道
    };
  },
  created() {
    window.addEventListener("resize", this.getHeight);
    // 生命周期函数
    this.send();
  },
  destroyed() {
    window.removeEventListener("resize", this.getHeight);
  },
  mounted() {
    this.getDistricts(); // 获取所有的区/县
  },
  methods: {
    handleSizeChange(val) {
      this.pageSize = val;
      this.send();
      console.log("每页显示" + val + "条记录");
    },
    getHeight() {
      this.tableConfig.height = window.innerHeight - 159; // 使表格高度自适应
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      this.send();
      console.log("this.currentPage=" + val);
    },
    // 后台管理人员查询方法
    submitFormSearch() {
      this.send();
    },
    // 导出表格数据
    exportTable() {},
    // 点击行编辑按钮触发事件
    handleEdit(index, row) {
      this.dialogFormVisible = true; // 设置dialog form表单加载
      this.formEdit = Object.assign({}, row); // 数据回显
    },
    // 点击行删除按钮触发事件
    handleDelete(index, row) {
      this.formEdit = Object.assign({}, row); // 数据回显
      this.$confirm("确定删除该管理员吗?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          axios({
            method: "post",
            url: "/system/deleteSystemAdminById",
            params: {
              id: this.formEdit.id,
              trueName: this.formEdit.trueName,
            },
          })
            .then((res) => {
              if (res.data.code === 60000) {
                this.$message({
                  message: res.data.msg,
                  type: "success",
                });
                this.send();
              } else {
                this.$message.error(res.data.msg);
              }
            })
            .catch((resp) => {
              console.log("请求失败：" + resp);
            });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },
    // 获取所有的区/县
    getDistricts() {
      axios({
        method: "get",
        url: "/region/listDistricts",
        params: {},
      })
        .then((res) => {
          res.data.data.forEach((o, index) => {
            this.districts.push({ label: o.district, value: o.district });
          });
        })
        .catch((resp) => {
          console.log("请求失败：" + resp);
        });
      console.log("区/县：" + this.districts);
    },
    // 获取当前districts 区/县的选中值
    currentSelectValue(value) {
      this.streets = [],
      axios({
        method: "get",
        url: "/region/listStreetsByDistrict",
        params: {
          street: value,
        },
      })
        .then((res) => {
          res.data.data.forEach((o, index) => {
            this.streets.push({ label: o.street, value: o.street });
          });
        })
        .catch((resp) => {
          this.loading = false;
          console.log("请求失败：" + resp);
        });
    },
    // 后台管理人员信息修改确认按钮点击事件
    dialogFormConfirm() {
      axios({
        method: "post",
        url: "/system/updateSystemAdmin",
        params: {
          id: this.formEdit.id,
          username: this.formEdit.username,
          password: this.formEdit.password,
          trueName: this.formEdit.trueName,
          mobile: this.formEdit.mobile,
          district: this.formEdit.district,
          street: this.formEdit.street,
          status: this.formEdit.status,
        },
      })
        .then((res) => {
          if (res.data.code === 40000) {
            console.log(res.data.code);
            this.$message.error(res.data.msg);
          } else if (res.data.code === 400001) {
            this.$message({
              message: res.data.msg,
              type: "success",
            });
            this.dialogFormVisible = false;
            this.send(); // 重新查询所有管理员信息
          }
        })
        .catch((resp) => {
          this.loading = false;
          console.log("请求失败：" + resp);
        });
    },
    // 获取所有的后台管理员
    send() {
      this.loading = true;
      axios({
        method: "get",
        url: "/system/listSystemAdmins",
        params: {
          pageSize: this.pageSize,
          pageNum: this.currentPage,
          trueName: this.formSearch.trueName,
          mobile: this.formSearch.phone,
        },
      })
        .then((res) => {
          console.log(res);
          this.consultBillList = res.data.data;
          this.pageTotal = res.data.total;
          this.loading = false;
        })
        .catch((resp) => {
          this.loading = false;
          console.log("请求失败：" + resp);
        });
    },
    toggleSelection(rows) {
      if (rows) {
        rows.forEach((row) => {
          this.$refs.multipleTable.toggleRowSelection(row);
        });
      } else {
        this.$refs.multipleTable.clearSelection();
      }
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
  },
};
</script>

<style scoped>
.Information {
  background-color: white;
}
.Information-title {
  width: 100%;
  height: auto;
  margin-bottom: 10px;
}
</style>