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
        <el-form-item label="订单编号">
          <el-input
            placeholder="请输入"
            v-model="formLabelSearch.searchName"
            @keyup.enter.native="handleSearch"
          ></el-input>
        </el-form-item>
         <el-form-item label="状态">
          <el-select
            v-model="formLabelSearch.orderStatus"
            placeholder="请选择状态"
          >
            <el-option
              label="全部"
              value=""
            ></el-option>
            <el-option
              label="已完成"
              value="0"
            ></el-option>
            <el-option
              label="维修中"
              value="1"
            ></el-option>
            <el-option
              label="已取消"
              value="2"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button
            type="primary"
            @click="handleSearch"
          >查询</el-button>
        </el-form-item>
      </el-form>
    </div>
    <div class="table-box">
      <div class="function">
        <el-button
          type="primary"
          @click="dialogAddVisible = true"
          v-if="save"
        >新增</el-button>
        <el-dialog
          title="新增"
          :visible.sync="dialogAddVisible"
          @open="open"
        >
          <el-form
            :model="formLabelAdd"
            :rules="rules"
            ref="formLabelAdd"
            label-position="right"
            label-width="80px"
          >
            <el-form-item
              label="设备编号"
              prop="maintainChargeId"
            >
              <el-select
                v-model="formLabelAdd.maintainChargeId"
                filterable
                :filter-method="getChargeList"
                placeholder="请选择"
              >
                <el-option
                  v-for="item in options1"
                  :key="item.id"
                  :label="item.chargeNumber"
                  :value="item.id"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item
              label="维修人员"
              prop="maintenancePersonnelId"
            >
              <el-select
                v-model="formLabelAdd.maintenancePersonnelId"
                placeholder="请选择"
              >
                <el-option
                  v-for="item in options2"
                  :key="item.id"
                  :label="item.text"
                  :value="item.id"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-form>
          <div
            slot="footer"
            class="dialog-footer"
          >
            <el-button @click="dialogAddVisible = false">取 消</el-button>
            <el-button
              type="primary"
              @click="handleAdd('formLabelAdd')"
            >确 定</el-button>
          </div>
        </el-dialog>
        <el-dialog
          title="编辑"
          :visible.sync="dialogEdit"
          @open="open"
        >
          <el-form
            :model="formEdit"
            :rules="rules"
            ref="formEdit"
            label-position="right"
            label-width="80px"
          >
            <el-form-item
              label="设备编号"
              prop="maintainChargeId"
            >
              <el-select
                v-model="formEdit.maintainChargeId"
                filterable
                :filter-method="getChargeList"
                placeholder="请选择"
              >
                <el-option
                  v-for="item in options1"
                  :key="item.id"
                  :label="item.chargeNumber"
                  :value="item.id"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item
              label="维修人员"
              prop="maintenancePersonnelId"
            >
              <el-select
                v-model="formEdit.maintenancePersonnelId"
                placeholder="请选择"
              >
                <el-option
                  v-for="item in options2"
                  :key="item.id"
                  :label="item.text"
                  :value="item.id"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-form>
          <div
            slot="footer"
            class="dialog-footer"
          >
            <el-button @click="dialogEdit = false">取 消</el-button>
            <el-button
              type="primary"
              @click="saveEdit1('formEdit')"
            >确 定</el-button>
          </div>
        </el-dialog>
        <el-dialog
          title="订单详情"
          :visible.sync="dialogEditVisible"
          @open="open"
        >
          <el-form
            :model="formLabelEdit"
            :rules="rules"
            ref="formLabelEdit"
            label-position="right"
            label-width="100px"
          >
            <el-form-item label="设备编号">
              <el-input
                placeholder="请输入"
                v-model="formLabelEdit.charge_number"
                readonly
              ></el-input>
            </el-form-item>
            <el-form-item label="维修人员">
              <span>{{formLabelEdit.name}}</span>
            </el-form-item>
            <el-form-item
              label="订单金额"
              prop="orderPrice"
            >
              <el-input
                placeholder="请输入"
                v-model="formLabelEdit.orderPrice"
              ></el-input>
            </el-form-item>
            <el-form-item
              label="使用库存"
              prop="chargeId"
            >
              <el-select
                v-model="formLabelEdit.chargeId"
                filterable
                :filter-method="getKuChargeList"
                placeholder="请选择"
              >
                <el-option
                  v-for="item in options3"
                  :key="item.id"
                  :label="item.chargeNumber"
                  :value="item.id"
                ></el-option>
              </el-select>
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
      </div>
      <el-table
        :data="userList"
        border
        style="width: 100%"
      >
        <el-table-column
          prop="order_number"
          label="订单编号"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="name"
          label="维修员姓名"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="charge_number"
          label="维修设备编号"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="tel"
          label="维修人员电话"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="order_status"
          label="订单状态"
          align="center"
        >
          <template slot-scope="scope">
            <span
              v-if="scope.row.order_status==0"
              style="color:#67C23A"
            >已完成</span>
            <span
              v-if="scope.row.order_status==1"
              style="color:#909399"
            >维修中</span>
            <span
              v-if="scope.row.order_status==2"
              style="color:#F56C6C"
            >已取消</span>
          </template>
        </el-table-column>
        <el-table-column
          prop="order_price"
          label="订单金额"
          align="center"
        ></el-table-column>
        <el-table-column
          label="操作"
          align="center"
          width="300"
        >
          <template slot-scope="scope">
            <el-button
              size="mini"
              @click="handleFinish(scope.$index, scope.row)"
              :disabled="scope.row.order_status==0||scope.row.order_status==2"
              v-if="selectOrderById"
            >维修完成</el-button>
            <el-button
              size="mini"
              @click="handleEdit(scope.$index, scope.row)"
              :disabled="scope.row.order_status==0 ||scope.row.order_status==2"
            >编辑订单</el-button>
            <el-button
              size="mini"
              @click="handleCancel(scope.$index, scope.row)"
              :disabled='scope.row.order_status==2 ||scope.row.order_status==0'
            >取消订单</el-button>
          </template>
        </el-table-column>
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
// import {
//   provinceAndCityData,
//   regionData,
//   provinceAndCityDataPlus,
//   regionDataPlus,
//   CodeToText,
//   TextToCode
// } from "element-china-area-data";
export default {
  data() {
    return {
      total: 1,
      total1: 1,
      page: 1,
      rows: 10,
      idx: -1,
      state: -1,
      title: "",
      flag: true,
      loading: true,
      shopAddress: [],
      dataPick: "",
      dialogEditVisible: false,
      dialogEdit: false,
      dialogAddVisible: false,
      dialogDetailVisible: false,
      userList: [],
      detailList: [],
      nameList: [],
      roleList: [],
      options1: [],
      options2: [],
      options3: [],
      props: {
        value: "label"
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
      formEdit: {},
      rules: {
        maintenancePersonnelId: [
          { required: true, message: "请选择人员", trigger: "change" }
        ],
        maintainChargeId: [
          { required: true, message: "请选择设备", trigger: "change" }
        ],
        name: [{ required: true, message: "请输入姓名", trigger: "blur" }],
        orderPrice: [
          { required: true, message: "请输入金额", trigger: "blur" }
        ],
        chargeId: [
          { required: true, message: "请选择设备编号", trigger: "change" }
        ]
      },
      save: true,
      selectOrderById: true
    };
  },
  mounted() {
    this.getUserList(this.formLabelAlign.page, this.formLabelAlign.rows);
    this.getChargeList();
    this.getKuChargeList();
    this.getBtnPms(this.$route.name);
    //新增权限
    this.btnList.some(item => {
      return (this.save = item.power == "maintenanceOrder_save");
    });
    //修改订单权限
    this.btnList.some(item => {
      return (this.selectOrderById = item.power == "maintenanceOrder_details");
    });
  },
  computed: {
    maintainChargeId() {
      return this.formLabelAdd.maintainChargeId;
    }
  },
  watch: {
    maintainChargeId() {
      this.getPersonnelList();
    }
  },
  methods: {
    //获取列表数据
    getUserList(page, rows) {
      console.log(rows);
      this.$axios
        .post(
          "/management/maintenanceOrder/selectList",
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
    // 获取设备编号
    getChargeList(val) {
      this.$axios
        .post(
          "/management/charge/getChargeCombo",
          this.$qs.stringify({
            page: 1,
            rows: 50,
            chargeNo: val,
            type: 0,
            status: 1
          })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.options1 = res.data.data.rows;
          }
        });
    },
    getChargeList1(val) {
      this.$axios
        .post(
          "/management/charge/selectChargeList",
          this.$qs.stringify({ page: 1, rows: 50, chargeNo: val })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.options1 = res.data.data.rows;
          }
        });
    },
    // 获取库存设备
    getKuChargeList(val) {
      this.$axios
        .post(
          "/management/charge/getChargeCombo",
          this.$qs.stringify({
            chargeNo: val,
            page: 1,
            rows: 50
          })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.options3 = res.data.data.rows;
          }
        });
    },
    // 获取维修人员
    getPersonnelList(val) {
      this.$axios
        .post(
          "/management/maintenancePersonnel/getComboByChargeId",
          this.$qs.stringify({
            page: 1,
            rows: 50,
            chargeId: this.formLabelAdd.maintainChargeId
          })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.options2 = res.data.data;
          }
        });
    },
    // dialog 打开前的回调
    open() {
      this.formLabelAdd = {};
    this.options2 =[]
    },
    //新增
    handleAdd(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$axios
            .post(
              "/management/maintenanceOrder/save",
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
    //完成订单
    handleFinish(index, rows) {
      this.formLabelEdit = {};
      this.options3=[]
      if (rows.order_status == 0) {
        this.$message.warning("订单已完成，请勿重复操作！");
        return false;
      }
      this.getChargeList1();
      console.log(rows);
      this.idx = rows.id;
      this.dialogEditVisible = true;
      // this.$axios
      //   .post(`/management/maintenanceOrder/selectDetails?id=${rows.id}`)
      //   .then(res => {
      //     if (res.status == 200) {
      //       console.log(res);
      //       this.formLabelEdit = {
      //         id: res.data.data.id,
      //         maintainChargeId: res.data.data.maintainChargeId,
      //         maintenancePersonnelId: res.data.data.maintenancePersonnelId
      //       };
      //     }
      //   });
      this.formLabelEdit = {
        id: rows.id,
        maintainChargeId: rows.maintain_charge_id,
        maintenancePersonnelId: rows.maintenance_personnel_id,
        charge_number: rows.charge_number,
        name: rows.name
      };
    },
    //确认完成
    saveEdit(form) {
      this.$refs[form].validate(valid => {
        if (valid) {
          this.formLabelEdit.orderStatus = 0;
          this.$axios
            .post(
              `/management/maintenanceOrder/save?id=${this.idx}`,
              this.$qs.stringify({
                id:rows.id,
                maintainChargeId: this.formLabelEdit.maintainChargeId,
                maintenancePersonnelId: this.formLabelEdit.maintenancePersonnelId,
                orderPrice: this.formLabelEdit.orderPrice,
                chargeId: this.formLabelEdit.chargeId,
              })
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
                this.$refs[form].resetFields();
                this.dialogEditVisible = false;
                this.getUserList(1, 10);
                this.formLabelEdit = {};
                
              }
            });
        }
      });
    },
    // 编辑订单
    handleEdit(index, rows) {
      this.dialogEdit = true;
      this.idx = rows.id;
      console.log(rows, "");
      // this.formEdit.maintainChargeId = rows.maintain_charge_id;
      // this.formEdit.maintenancePersonnelId = rows.maintenance_personnel_id;
      this.$set(this.formEdit, "maintainChargeId", rows.maintain_charge_id);
      this.$set(
        this.formEdit,
        "maintenancePersonnelId",
        rows.maintenance_personnel_id
      );
      setTimeout(() => {
        this.options1 = [
        { chargeNumber: rows.charge_number, id: rows.maintain_charge_id }
      ];
      this.options2 = [{ text: rows.name, id: rows.maintenance_personnel_id }];
      }, 100);
    },
    saveEdit1(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$axios
            .post(
              `/management/maintenanceOrder/save?id=${this.idx}`,
              this.$qs.stringify(this.formEdit)
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
                  message: `编辑成功!`
                });
                this.dialogEdit = false;
                this.getUserList(1, 10);
                this.$refs[formName].resetFields();
                this.formEdit = {};
              }
            });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    handleCancel(index, rows) {
      console.log(rows);
      this.$confirm("此操作取消该订单, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        this.$axios
          .post(`/management/maintenanceOrder/save?id=${rows.id}&orderStatus=2`)
          .then(res => {
            if (res.status == 200) {
              this.$message.success("已取消");
              this.getUserList(1, 10);
            }
          });
      });
    },
    // 查询
    handleSearch() {
      this.formLabelSearch.page = 1;
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
      this.rows = val;
      this.getDetailList(this.page, this.rows);
    },
    handleCurrentChange1(val) {
      this.page = val;
      this.getDetailList(this.page, this.rows);
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