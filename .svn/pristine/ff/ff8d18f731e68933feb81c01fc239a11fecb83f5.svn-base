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
        <el-form-item label="商家名称">
          <el-input
            placeholder="请输入"
            v-model="formLabelSearch.searchName"
            @keyup.enter.native="handleSearch"
          ></el-input>
        </el-form-item>
        <el-form-item label="状态">
          <el-select
            v-model="formLabelSearch.status"
            placeholder="请选择状态"
          >
            <el-option
              label="全部"
              value=""
            ></el-option>
            <el-option
              label="待处理"
              value="0"
            ></el-option>
            <el-option
              label="处理中"
              value="1"
            ></el-option>
            <el-option
              label="已完成"
              value="2"
            ></el-option>
            <el-option
              label="已忽略"
              value="3"
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
        <el-button
          type="primary"
          @click="handleExport"
          v-if="createExcel"
        >批量导出</el-button>
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
              label="商家"
              prop='shopId'
            >
              <el-select
                v-model="formLabelAdd.shopId"
                filterable
                :filter-method="getChargeList"
                placeholder="请选择"
              >
                <el-option
                  v-for="item in options1"
                  :key="item.shopid"
                  :label="item.shopname"
                  :value="item.shopid"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item
              label="反馈内容"
              prop="type"
            >
              <el-select
                v-model="formLabelAdd.type"
                placeholder="请选择"
              >
                <el-option
                  v-for="item in options2"
                  :key="item.id"
                  :label="item.problemDescribe"
                  :value="item.problemNo"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="备注">
              <el-input
                type="textarea"
                :rows="2"
                placeholder="请输入内容"
                v-model="formLabelAdd.message"
              ></el-input>
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
          title="维修"
          :visible.sync="dialogFixVisible"
          @open="open"
        >
          <el-form
            :model="formLabelFix"
            :rules="rules"
            ref="formLabelFix"
            label-position="right"
            label-width="80px"
          >
            <el-form-item label="设备编号">
              <el-select
                v-model="formLabelFix.maintainChargeId"
                filterable
                :filter-method="getChargeList2"
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
            <el-form-item label="维修人员">
              <el-select
                v-model="formLabelAdd.maintenancePersonnelId"
                placeholder="请选择"
              >
                <el-option
                  v-for="item in options4"
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
            <el-button @click="dialogFixVisible = false">取 消</el-button>
            <el-button
              type="primary"
              @click="handleFix('formLabelFix')"
            >确 定</el-button>
          </div>
        </el-dialog>
        <el-dialog
          title="补货"
          :visible.sync="dialogReloadVisible"
          @open='open'
        >
          <el-form
            :model="formLabelReload"
            :rules="rules"
            ref="formLabelReload"
            label-position="right"
            label-width="80px"
          >
            <el-form-item
              label="设备类型"
              prop="facilityType"
            >
              <el-radio
                v-model="formLabelReload.facilityType"
                label="0"
              >充电宝</el-radio>
              <el-radio
                v-model="formLabelReload.facilityType"
                label="1"
              >充电柜</el-radio>
            </el-form-item>

            <el-form-item label="设备编号">
              <el-select
                v-model="formLabelReload.facilityNo"
                multiple
                filterable
                remote
                reserve-keyword
                placeholder="请输入关键词"
                :filter-method="getChargeList3"
              >
                <el-option
                  v-for="item in options5"
                  :key="item.id"
                  :label="item.chargeNumber"
                  :value="item.chargeNumber"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="补货数量">
              <el-input
                v-model="formLabelReload.num"
                disabled
              ></el-input>
            </el-form-item>
            <!-- <el-form-item label="商家id">
              <el-input v-model="formLabelReload.shopId"></el-input>
            </el-form-item> -->
          </el-form>
          <div
            slot="footer"
            class="dialog-footer"
          >
            <el-button @click="dialogReloadVisible = false">取 消</el-button>
            <el-button
              type="primary"
              @click="handleReload('formLabelReload')"
            >确 定</el-button>
          </div>
        </el-dialog>
      </div>
      <el-table
        :data="userList"
        border
        style="width: 100%"
        @selection-change="handleSelectionChange"
        :row-key="getRowKeys"
      >
        <el-table-column
          type="selection"
          width="55"
          align="center"
          reserve-selection
        ></el-table-column>
        <!-- <el-table-column prop="id" label="id" align="center"></el-table-column> -->
        <el-table-column
          prop="shop_name"
          label="店铺名称"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="shop_address"
          label="所在区域"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="problem_describe"
          label="反馈内容"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="create_time"
          label="反馈时间"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="create_time"
          label="反馈状态"
          align="center"
        >
          <template slot-scope="scope">
            <span
              v-if="scope.row.status==0"
              style="color:#E6A23C"
            >待处理</span>
            <span
              v-if="scope.row.status==1"
              style="color:#E6A23C"
            >处理中</span>
            <span
              v-if="scope.row.status==2"
              style="color:#67C23A"
            >已完成</span>
            <span
              v-if="scope.row.status==3"
              style="color:#F56C6C"
            >已忽略</span>
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
              @click="handleEdit(scope.$index, scope.row)"
              :disabled="scope.row.status ===2"
            >编辑</el-button>
            <el-button
              size="mini"
              @click="dialogFixVisible = true"
              :disabled="scope.row.status!=0"
            >维修</el-button>
            <el-button
              size="mini"
              @click="dialogReloadVisible = true"
              :disabled="scope.row.status!=0"
            >补货</el-button>
            <!-- <el-button size="mini" @click="handleDelete(scope.$index, scope.row)">删除</el-button> -->
          </template>
        </el-table-column>
      </el-table>
      <el-dialog
        title="编辑"
        :visible.sync="dialogEditVisible"
        append-to-body
        @open="open"
      >
        <el-form
          :model="formLabelEdit"
          label-position="right"
          label-width="80px"
        >
          <el-form-item label="反馈内容">
            <el-input
              type="textarea"
              :rows="2"
              placeholder=""
              v-model="formLabelEdit.problem_describe"
              disabled
            ></el-input>
          </el-form-item>
        </el-form>

        <div
          slot="footer"
          class="dialog-footer"
        >
          <el-button @click="dialogEditVisible = false">取 消</el-button>
          <el-button
            type="primary"
            @click="saveEdit(2)"
            v-if="dispose"
          >已处理</el-button>
          <el-button
            type="primary"
            @click="saveEdit(3)"
            v-if="dispose"
          >忽 略</el-button>
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
    return {
      total: 1,
      total1: 1,
      page: 1,
      rows: 10,
      idx: -1,
      status: -1,
      state: -1,
      title: "",
      flag: true,
      loading: true,
      options1: [],
      options2: [],
      options3: [],
      options4: [],
      options5: [],
      shopAddress: [],
      multipleSelection: [],
      getRowKeys(row) {
        return row.id;
      },
      dataPick: "",
      dialogEditVisible: false,
      dialogAddVisible: false,
      dialogFixVisible: false,
      dialogReloadVisible: false,
      dialogDetailVisible: false,
      userList: [],
      detailList: [],
      nameList: [],
      roleList: [],
      gridData: [],
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
      formLabelFix: {},
      formLabelReload: {},
      formLabelEdit: {},
      rules: {
        shopId: [{ required: true, message: "请选择商家", trigger: "change" }],
        type: [{ required: true, message: "请选择反馈内容", trigger: "change" }]
      },
      save: true,
      createExcel: true,
      dispose: true
    };
  },
  mounted() {
    this.getUserList(this.formLabelAlign.page, this.formLabelAlign.rows);
    this.getChargeList();
    this.getChargeList2();
    this.getChargeList3();
    this.getfeedbackList();
    this.getBtnPms(this.$route.name);
    //新增权限
    this.btnList.some(item => {
      return (this.save = item.power == "merchantFeedback_save");
    });
    //导出权限
    this.btnList.some(item => {
      return (this.createExcel = item.power == "merchantFeedback_createExcel");
    });
    //处理权限
    this.btnList.some(item => {
      return (this.dispose = item.power == "merchantFeedback_dispose");
    });
  },
  computed: {
    maintainChargeId() {
      return this.formLabelFix.maintainChargeId;
    },
    numAdd() {
      return this.formLabelReload.facilityNo;
    }
  },
  watch: {
    maintainChargeId() {
      this.getPersonnelList();
    },
    numAdd(val) {
      this.formLabelReload.num = val.length;
    }
  },
  methods: {
    //  全选
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
    // 获取设备编号
    getChargeList2(val) {
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
            this.options3 = res.data.data.rows;
          }
        });
    },
    //获取充电柜列表
    getChargeList3(val) {
      this.$axios
        .post(
          "/management/charge/getChargeCombo",
          this.$qs.stringify({
            chargeNo: val,
            type: this.formLabelReload.facilityType,
            page: 1,
            rows: 50
          })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.options5 = res.data.data.rows;
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
            chargeId: this.formLabelFix.maintainChargeId
          })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.options4 = res.data.data;
          }
        });
    },
    //获取列表数据
    getUserList(page, rows) {
      console.log(rows);
      this.$axios
        .post(
          "/management/merchantFeedback/selectList",
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
    // 获取商家列表
    getChargeList(val) {
      this.$axios
        .post(
          `/management/shop/selectShopList`,
          this.$qs.stringify({ page: 1, rows: 50, name: val })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.options1 = res.data.data.rows;
          }
        });
    },
    // 获取反馈列表
    getfeedbackList() {
      this.$axios
        .get("/management/merchantFeedback/getProblemData")
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.options2 = res.data.data;
          }
        });
    },
    // dialog 打开前的回调
    open() {
      this.shopAddress = [];
      this.formLabelAdd = {};
    },
    //新增
    handleAdd(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$axios
            .post(
              "/management/merchantFeedback/save",
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
    // 维修
    handleFix(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$axios
            .post(
              "/management/maintenanceOrder/save",
              this.$qs.stringify(this.formLabelFix)
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
                this.dialogFixVisible = false;
                this.getUserList(1, 10);
                this.$refs[formName].resetFields();
                this.formLabelFix = {};
              }
            });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    //补货
    handleReload(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.formLabelReload.facilityNo = this.formLabelReload.facilityNo.join(
            ","
          );
          // this.fo
          this.$axios
            .post(
              "/management/replenishmentOrder/save",
              this.$qs.stringify(this.formLabelReload)
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
                this.dialogReloadVisible = false;
                this.getUserList(1, 10);
                this.$refs[formName].resetFields();
                this.formLabelReload = {};
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
      if (rows.status == 2) {
        this.$message.error("反馈已处理");
        return false;
      }
      this.formLabelEdit = {};
      console.log(rows);
      this.idx = rows.id;
      this.status = rows.status
      this.formLabelEdit = {
        problem_describe: rows.problem_describe
      };
      this.dialogEditVisible = true;
    },
    //确认编辑
    saveEdit(state) {
      if(state==3){
        if(this.status==3){
          this.$message.warning('已忽略反馈，请勿重复操作！')
          return
        }
      }
      this.$axios
        .post(
          `/management/merchantFeedback/dispose?id=${this.idx}&status=${state}`,
          this.$qs.stringify(this.formLabelEdit)
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            if (res.data.code == 201) {
              this.$message({
                type: "warning",
                message: `反馈已完成，不允许修改`
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
              message: `操作成功!`
            });
            this.dialogEditVisible = false;
            this.getUserList(1, 10);
            this.formLabelEdit = {};
          }
        });
    },
    // 查询
    handleSearch() {
      this.getUserList(1, this.formLabelAlign.rows);
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
      let ids = [];
      this.multipleSelection.filter(item => {
        ids.push(item.id);
      });
      ids = ids.join(",");
      console.log(ids);
      if(ids==''){
        this.$message.error('请勾选需要导出的数据')
        return
      }
      this.$axios
        .post(`/management/merchantFeedback/createExcel`,this.$qs.stringify({ids:ids}),{
          responseType: "blob"
        })
        .then(res => {
          let blob = new Blob([res.data], {
            type:
              "application/vnd.ms-excel,application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
          });
          if (window.navigator.msSaveOrOpenBlob) {
            navigator.msSaveBlob(blob);
          } else {
            let elink = document.createElement("a");
            elink.download = "商家反馈报表.xls";
            elink.style.display = "none";
            elink.href = URL.createObjectURL(blob);
            document.body.appendChild(elink);
            elink.click();
            document.body.removeChild(elink);
          }
        });
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