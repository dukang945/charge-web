<template>
  <div class="contain-box">
    <div class="table-box">
      <el-form
        label-position="right"
        label-width="80px"
        :model="formLabelSearch"
        :inline="true"
        @submit.native.prevent
      >
        <el-form-item label="门店名称">
          <el-input
            placeholder="请输入名称"
            v-model="formLabelSearch.shopName"
            @keyup.enter.native="handleSearch"
          ></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleSearch">查询</el-button>
          <!-- <el-button @click="handleReset">重置</el-button> -->
        </el-form-item>
      </el-form>
    </div>
    <div class="table-box">
      <div class="function">
        <!-- <el-button type="primary" @click="handleExport">导出数据</el-button> -->
        <span>月度销售指标完成度{{accomplishPerformance}}\{{monthPerformance}}</span>
        <el-progress
          :percentage="accomplishPerformance/monthPerformance*100"
          text-inside
          :stroke-width="20"
          v-if="accomplishPerformance!=0"
        ></el-progress>
        <el-progress :percentage="0" text-inside :stroke-width="20" v-else></el-progress>
      </div>
      <el-table :data="userList" border style="width: 100%">
        <el-table-column prop="id" label="商家id" align="center"></el-table-column>
        <el-table-column prop="shop_name" label="店铺名称" align="center"></el-table-column>
        <el-table-column prop="shop_detailed_address" label="店铺地址" align="center"></el-table-column>
        <el-table-column prop="charge_num" label="充电柜数量" align="center"></el-table-column>
        <el-table-column prop="充电柜数量" label="分成收益" align="center"></el-table-column>
        <!-- <el-table-column label="操作" align="center" width="200">
          <template slot-scope="scope">
            <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
            <el-button size="mini" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
          </template>
        </el-table-column>-->
      </el-table>
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :page-sizes="[10, 20]"
        :current-page="formLabelAlign.page"
        :page-size="10"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
      ></el-pagination>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      total: 1,
      total1: 1,
      page: 1,
      rows: 1,
      idx: -1,
      state: -1,
      title: "",
      flag: true,
      loading: true,
      dataPick: "",
      accomplishPerformance: 0,
      monthPerformance: 0,
      dialogEditVisible: false,
      dialogAddVisible: false,
      onlineDialogVisible: false,
      userList: [],
      detailList: [],
      nameList: [],
      roleList: [],
      formLabelAlign: {
        page: 1,
        rows: 10
      },
      formLabelSearch: {
        page: 1,
        rows: 10
      },
      formLabelAdd: {},
      formLabelEdit: {},
      rules: {
        loginName: [{ required: true, message: "请输入账号", trigger: "blur" }],
        password: [{ required: true, message: "请输入密码", trigger: "blur" }],
        name: [{ required: true, message: "请输入姓名", trigger: "blur" }]
      }
    };
  },
  mounted() {
    this.getUserList(this.formLabelSearch.page, this.formLabelSearch.rows);
    this.getPerformance();
    this.getBtnPms(this.$route.name)
  },
  methods: {
    //获取列表数据
    getUserList(page, rows) {
      const role = sessionStorage.getItem("id");
      this.formLabelSearch.salesmanId = role;
      this.$axios
        .post(
          "/management/salesman/selectSalesmanMoneyList",
          this.$qs.stringify(this.formLabelSearch)
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            if (res.data.code == 200) {
              this.userList = res.data.data.rows;
              this.total = res.data.data.total;
            } else {
              this.$message({
                type: "warning",
                message: `操作异常!`
              });
            }
          }
        });
    },
    // 获取业务员完成指标
    getPerformance() {
      this.$axios
        .get(
          `/management/salesman/getMonthPerformance?salesmanId=${sessionStorage.getItem(
            "id"
          )}`
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.monthPerformance = res.data.data.monthPerformance;
            this.accomplishPerformance = res.data.data.accomplishPerformance;
          }
        });
    },
    //新增
    handleAdd(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$axios
            .post(
              "/management/sysUser/saveSysUser",
              this.$qs.stringify(this.formLabelAdd)
            )
            .then(res => {
              if (res.status == 200) {
                if (res.data.code == 506) {
                  this.$message({
                    type: "warning",
                    message: `账号重复`
                  });
                  return false;
                } else if (res.data.code == 507) {
                  this.$message({
                    type: "warning",
                    message: `手机号重复`
                  });
                  return false;
                }
                this.$message({
                  type: "success",
                  message: `添加成功!`
                });
                this.dialogAddVisible = false;
                this.getUserList(1, 10);
                this.$refs[formName].resetFields();
                this.formLabelAdd = {};
              }
            });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    //编辑
    handleEdit(index, rows) {
      console.log(rows);
      this.dialogEditVisible = true;
      this.formLabelEdit = {
        id: rows.id,
        loginName: rows.loginName,
        password: rows.password,
        name: rows.name
      };
    },
    //确认编辑
    saveEdit(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$axios
            .post(
              "/management/sysUser/saveSysUser",
              this.$qs.stringify(this.formLabelEdit)
            )
            .then(res => {
              if (res.status == 200) {
                console.log(res);
                if (res.data.code == 506) {
                  this.$message({
                    type: "warning",
                    message: `账号重复`
                  });
                  return false;
                } else if (res.data.code == 507) {
                  this.$message({
                    type: "warning",
                    message: `手机号重复`
                  });
                  return false;
                }
                this.$message({
                  type: "success",
                  message: `编辑成功!`
                });
                this.dialogEditVisible = false;
                this.getUserList(1, 10);
                this.$refs[formName].resetFields();
                this.formLabelEdit = {};
              }
            });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },

    handleDelete(index, rows) {
      console.log(rows);
      this.$confirm("此操作将永久删除该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        this.$axios
          .get(`/management/sysUser/deleteSysUser?id=${rows.id}`)
          .then(res => {
            if (res.status == 200) {
              this.$message.success("删除成功");
              this.getUserList(1, 10);
            }
          });
      });
    },
    // 查询
    handleSearch() {
      this.getUserList(1, this.formLabelSearch.rows);
    },
    //重置
    handleReset() {
      this.formLabelSearch = this.formLabelAlign = {
        phone: "",
        loginAccount: "",
        roleName: "",
        state: "",
        page: 1,
        rows: 10
      };
    },
    //导出数据
    // handleExport() {
    //   window.location.href = `http://192.168.12.11:8401/management/sysAccount/exportExcel?phone=${
    //     this.formLabelAlign.phone
    //   }&loginAccount=${this.formLabelAlign.loginAccount}&roleName=${
    //     this.formLabelAlign.roleName
    //   }&state=${this.formLabelAlign.state}`;
    // },
    //分页
    handleSizeChange(val) {
      this.formLabelSearch.rows = val;
      this.getUserList(this.formLabelSearch.page, this.formLabelSearch.rows);
    },
    handleCurrentChange(val) {
      this.formLabelSearch.page = val;
      this.getUserList(this.formLabelSearch.page, this.formLabelSearch.rows);
    }
  }
};
</script>

<style scoped lang='scss'>
// /deep/ .el-dialog .el-form{
//   width: 800px;
// }
// .left-form{
//   margin-right: 130px;
// }
.black {
  color: #fff;
  opacity: 0;
}
</style>