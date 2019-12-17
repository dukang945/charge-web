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
        <el-form-item label="账号" >
          <el-input
            placeholder="请输入"
            v-model="formLabelSearch.loginName"
            @keyup.enter.native="handleSearch"
          ></el-input>
        </el-form-item>
        <el-form-item label="在职状态">
          <el-select
            v-model="formLabelSearch.status"
            placeholder="请选择状态"
          >
            <el-option
              label="全部"
              value=""
            ></el-option>
            <el-option
              label="在职"
              value="0"
            ></el-option>
            <el-option
              label="离职"
              value="2"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="月份筛选">
          <el-date-picker
            v-model="formLabelSearch.time"
            type="month"
            value-format='yyyy-MM'
            placeholder="选择月"
          >
          </el-date-picker>
        </el-form-item>
        <el-form-item>
          <el-button
            type="primary"
            @click="handleSearch"
          >查询</el-button>
          <!-- <el-button @click="handleReset">重置</el-button> -->
        </el-form-item>
      </el-form>
    </div>
    <div class="table-box">
      <div class="function">
        <!-- <el-button type="primary" @click="handleExport">批量导出</el-button> -->
      </div>
      <el-table
        :data="userList"
        border
        style="width: 100%"
      >
        <el-table-column
          prop="loginname"
          label="业务员账号"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="name"
          label="姓名"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="name"
          label="在职状态"
          align="center"
        >
          <template slot-scope="scope">
            <span v-if="scope.row.status==0">在职</span>
            <span v-if="scope.row.status==2">离职</span>
          </template>
        </el-table-column>
        <el-table-column
          prop="city"
          label="所在城市"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="divideratio"
          label="分成比例"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="allamount"
          label="指标完成度"
          align="center"
        >
          <template
            slot-scope="scope"
            v-if="scope.row.allperformance"
          > 
            <el-progress
              :percentage="scope.row.accomplishperformance/scope.row.allperformance * 100>0?(scope.row.accomplishperformance/scope.row.allperformance * 100).toFixed(2):0"
              text-inside
              :stroke-width="12"
              style="width:70%;float:left"
              v-if="scope.row.accomplishperformance != null"
            >
            </el-progress>
            <span style="font-size:12px;float:right;line-height:1">{{scope.row.accomplishperformance}}/{{scope.row.allperformance}}</span>

          </template>
        </el-table-column>
        <el-table-column
          label="操作"
          align="center"
          width="300"
        >
          <template slot-scope="scope">
            <el-button
              size="mini"
              @click="showSaleList(scope.row)"
              v-if="selectMoneyList"
            >已售设备</el-button>
            <el-button
              size="mini"
              @click="handleEdit(scope.row)"
              v-if="edit"
            >编辑</el-button>
            <el-button
              size="mini"
              @click="handleMove(scope.$index, scope.row)"
              :disabled="scope.row.status==0"
            >转移设备</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-dialog
        title="已售设备"
        :visible.sync="dialogTableVisible"
      >
        <el-table :data="gridData">
          <el-table-column
            property="chargeid"
            label="设备号"
            width="150"
          ></el-table-column>
          <el-table-column
            property="allmoney"
            label="总收益"
            width="200"
          ></el-table-column>
          <el-table-column
            property="usetime"
            label="销售日期"
          ></el-table-column>
          <el-table-column
            property="address"
            label="区域"
          ></el-table-column>
        </el-table>
        <el-pagination
          @size-change="handleSizeChange1"
          @current-change="handleCurrentChange1"
          :page-sizes="[10, 20]"
          :current-page="1"
          :page-size="10"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total1"
        ></el-pagination>
      </el-dialog>
      <el-dialog
        title="编辑"
        :visible.sync="dialogEditVisible"
        append-to-body
      >
        <el-form
          :model="formLabelEdit"
          :rules="rules"
          ref="formLabelEdit"
          label-position="right"
          label-width="100px"
        >
          <el-form-item label="账号(手机)" prop='loginName'>
            <el-input v-model.number="formLabelEdit.loginName"></el-input>
          </el-form-item>

          <el-form-item label="城市" prop='city'>
            <el-input v-model="formLabelEdit.city"></el-input>
          </el-form-item>
          <el-form-item label="分成比例" prop='divideRatio'>
            <el-input v-model="formLabelEdit.divideRatio"></el-input>
          </el-form-item>
          <el-form-item label="月度指标" prop='monthPerformance'>
            <el-input v-model="formLabelEdit.monthPerformance"></el-input>
          </el-form-item>
        </el-form>
        <div
          slot="footer"
          class="dialog-footer"
        >
          <el-button @click="dialogEditVisible = false">取 消</el-button>
          <el-button
            type="primary"
            @click="saveEdit('formLabelEdit')"
          >确 定</el-button>
        </div>
      </el-dialog>
      <el-dialog
        title="转移设备"
        :visible.sync="dialogMoveVisible"
        append-to-body
      >
        <el-form
          :model="formLabelMove"
          :rules="rules"
          ref="formLabelMove"
          label-position="right"
          label-width="100px"
        >
          <el-form-item label="转移人员">
            <el-select
              v-model="formLabelMove.newUserId"
              filterable
              :filter-method="getUserList2"
              placeholder="请选择"
            >
              <el-option
                v-for="item in userList2"
                :key="item.shopid"
                :label="item.name"
                :value="item.id"
              >
                <span style="float: left">{{ item.name }}</span>
                <span style="float: right; color: #8492a6; font-size: 13px">{{ item.loginname }}</span></el-option>
            </el-select>
          </el-form-item>
        </el-form>
        <div
          slot="footer"
          class="dialog-footer"
        >
          <el-button @click="dialogMoveVisible = false">取 消</el-button>
          <el-button
            type="primary"
            @click="saveMove"
          >确 定</el-button>
        </div>
      </el-dialog>
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
    var validatePass3 = (rule, value, callback) => {
      console.log(value);
      var reg = /^1[34578]\d{9}$/;
      if (!value) {
        callback(new Error("请输入账号"));
      } else if (!reg.test(value)) {
        callback(new Error("请输入正确手机号!"));
      } else {
        callback();
      }
    };
    return {
      total: 1,
      total1: 1,
      page1: 1,
      rows1: 10,
      idx: -1,
      state: -1,
      title: "",
      selectMoneyList: true,
      edit: true,
      flag: true,
      loading: true,
      dataPick: "",
      dialogTableVisible: false,
      dialogAddVisible: false,
      dialogEditVisible: false,
      dialogMoveVisible: false,
      onlineDialogVisible: false,
      userList: [],
      userList2: [],
      detailList: [],
      nameList: [],
      roleList: [],
      gridData: [],
      multipleSelection: [],
      getRowKeys(row) {
        return row.id;
      },
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
      formLabelMove: {},
      rules: {
        loginName: [
          { required: true, validator: validatePass3, trigger: "blur" }
        ],
        divideRatio: [{ required: true, message: "请输入分成比例", trigger: "blur" }],
        monthPerformance: [{ required: true, message: "请输入指标", trigger: "blur" }],
        city: [{ required: true, message: "请输入城市", trigger: "blur" }]
      }
    };
  },
  mounted() {
    this.getUserList(this.formLabelSearch.page, this.formLabelSearch.rows);
    this.getBtnPms(this.$route.name);
    //查看已售设备权限
    this.btnList.some(item => {
      return (this.selectMoneyList = item.power == "salesman_selectMoneyList");
    });
    //编辑权限
    this.btnList.some(item => {
      return (this.edit = item.power == "salesman_edit");
    });
  },
  methods: {
    //  全选
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
    //获取列表数据
    getUserList(page, rows) {
      console.log(rows);
      this.$axios
        .post(
          "/management/salesman/selectList",
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
    getUserList2(val) {
      this.$axios
        .post(
          "/management/salesman/selectList",
          this.$qs.stringify({ status: 0, username: val, page: 1, rows: 50 })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            if (res.data.code == 200) {
              this.userList2 = res.data.data.rows;
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
    handleEdit(rows) {
      this.formLabelEdit = {};
      console.log(rows);
      this.idx = rows.id;
      this.dialogEditVisible = true;
      this.$axios
        .get(`/management/sysUser/getSysUserDetails?id=${rows.id}`)
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.formLabelEdit = {
              loginName: res.data.data.loginName,
              city: res.data.data.salesmanDetails.city,
              monthPerformance: res.data.data.salesmanDetails.monthPerformance,
              divideRatio: res.data.data.salesmanDetails.divideRatio
            };
          }
        });
    },
    //确认编辑
    saveEdit(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          console.log(this.idx);
          this.$axios
            .post(
              `/management/salesman/setSalesmanDetails?id=${this.idx}`,
              this.$qs.stringify(this.formLabelEdit)
            )
            .then(res => {
              if (res.status == 200) {
                console.log(res);
                if (res.data.code == 1) {
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
    //查看已售设备
    showSaleList(rows) {
      console.log(rows);
      this.dialogTableVisible = true;
      this.idx = rows.id
      this.$axios
        .post(
          `/management/salesman/selectMoneyList?salesmanId=${rows.id}`,
          this.$qs.stringify({ page: this.page1, rows: this.rows1 })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.gridData = res.data.data.rows;
            this.total1 = res.data.data.total;
          }
        });
    },
    // 转移设备
    handleMove(index, rows) {
      this.dialogMoveVisible = true;
      this.idx = rows.id;
    },
    saveMove() {
      this.$axios
        .post(
          `/management/charge/associatePermisson?oldUserId=${this.idx}&newUserId=${this.formLabelMove.newUserId}`
        )
        .then(res => {
          if (res.status == 200) {
            this.$message.success("转移成功");
            this.dialogMoveVisible = false;
            this.getUserList(1, 10);
          }
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
    handleExport() {
      window.location.href = `http://192.168.6.103:8401/management/wallet/createWalletExcel?username=${this.formLabelSearch.username}`;
       
    },
    //分页
    handleSizeChange(val) {
      this.formLabelSearch.rows = val;
      this.getUserList(this.formLabelSearch.page, this.formLabelSearch.rows);
    },
    handleCurrentChange(val) {
      this.formLabelSearch.page = val;
      this.getUserList(this.formLabelSearch.page, this.formLabelSearch.rows);
    },
    handleSizeChange1(val) {
      this.rows1 = val;
      this.$axios
        .post(
          `/management/salesman/selectMoneyList?salesmanId=${this.idx}`,
          this.$qs.stringify({ page: this.page1, rows: this.rows1 })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.gridData = res.data.data.rows;
            this.total1 = res.data.data.total;
          }
        });
    },
    handleCurrentChange1(val) {
      this.page1 = val;
      this.$axios
        .post(
          `/management/salesman/selectMoneyList?salesmanId=${this.idx}`,
          this.$qs.stringify({ page: this.page1, rows: this.rows1 })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.gridData = res.data.data.rows;
            this.total1 = res.data.data.total;
          }
        });
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