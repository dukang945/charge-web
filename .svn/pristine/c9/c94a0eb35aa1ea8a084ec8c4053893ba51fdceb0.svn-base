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
        <el-form-item label="设备编号">
          <el-input
            placeholder="请输入设备编号"
            v-model="formLabelSearch.chargeNo"
            @keyup.enter.native="handleSearch"
          ></el-input>
        </el-form-item>
        <el-form-item label="设备状态">
          <el-select
            v-model="formLabelSearch.status"
            placeholder="请选择状态"
          >
            <el-option
              label="全部"
              value=""
            ></el-option>
            <el-option
              label="正在使用"
              value="3"
            ></el-option>
            <el-option
              label="在库"
              value="4"
            ></el-option>
            <el-option
              label="报废"
              value="1"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="运行状态">
          <el-select
            v-model="formLabelSearch.faultStatus"
            placeholder="请选择状态"
          >
            <el-option
              label="全部"
              value="0"
            ></el-option>
            <el-option
              label="正常"
              value="2"
            ></el-option>
            <el-option
              label="故障"
              value="1"
            ></el-option>
          </el-select>
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
        <el-button
          type="primary"
          @click="dialogAddVisible = true"
          v-if="save"
        >新增</el-button>
        <el-dialog
          title="新增"
          :visible.sync="dialogAddVisible"
          @open='open'
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
              prop="chargeNumber"
            >
              <el-input v-model="formLabelAdd.chargeNumber"></el-input>
            </el-form-item>
            <el-form-item
              label="设备类型"
              prop="type"
            >
              <el-radio
                v-model="formLabelAdd.type"
                label="1"
              >充电宝</el-radio>
              <el-radio
                v-model="formLabelAdd.type"
                label="0"
              >充电柜</el-radio>
            </el-form-item>
            <el-form-item label="所在区域">
              <el-cascader
                size="large"
                :options="options2"
                v-model="shopAddress"
                @change="addressChange"
                :props="props"
              ></el-cascader>
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
         <el-dialog title="维修" :visible.sync="dialogFixVisible" @open="open">
          <el-form
            :model="formLabelFix"
            :rules="rules"
            ref="formLabelFix"
            label-position="right"
            label-width="80px"
          >
            <el-form-item label="设备编号" prop="maintainChargeId">
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
            <el-form-item label="维修人员" prop="maintenancePersonnelId">
              <el-select v-model="formLabelAdd.maintenancePersonnelId" placeholder="请选择">
                <el-option
                  v-for="item in options4"
                  :key="item.id"
                  :label="item.text"
                  :value="item.id"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFixVisible = false">取 消</el-button>
            <el-button type="primary" @click="handleFix('formLabelFix')">确 定</el-button>
          </div>
        </el-dialog>
      </div>
      <el-table
        :data="userList"
        border
        style="width: 100%"
        @filter-change="filterTag"
      >
        <el-table-column
          prop="charge_number"
          label="设备编号"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="address"
          label="所在城市"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="shopId"
          label="设备类型"
          align="center"
        >
          <template slot-scope="scope">
            <span v-if="scope.row.type==1">充电宝</span>
            <span v-if="scope.row.type==0">充电柜</span>
          </template>
        </el-table-column>
        <el-table-column
          prop="loginName"
          label="设备状态"
          align="center"
        >
          <template slot-scope="scope">
            <span
              v-if="scope.row.status==3"
              style="color:green"
            >使用中</span>
            <span v-if="scope.row.status==4">在库</span>
            <span
              v-if="scope.row.status==1"
              style="color:red"
            >报废</span>
            <span
              v-if="scope.row.status==2 && scope.row.shop_id"
              style="color:green"
            >使用中</span>
            <span v-if="scope.row.status==2 && !scope.row.shop_id">在库</span>
            <span
              v-if="scope.row.status==0 && scope.row.shop_id"
              style="color:green"
            >使用中</span>
            <span v-if="scope.row.status==0 && !scope.row.shop_id">在库</span>
          </template>
        </el-table-column>
        <el-table-column
          prop="loginName"
          label="运行状态"
          align="center"
        >
          <template slot-scope="scope">
            <span
              v-if="scope.row.num==null||0"
              style="color:green"
            >正常</span>
            <span
              v-else
              style="color:red"
            >故障</span>
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
              v-if="details"
            >编辑</el-button>
            <el-button
              size="mini"
              @click="handleDelete(scope.$index, scope.row)"
              v-if="scrap"
              :disabled="scope.row.status==1"
            >报废</el-button>
            <el-button size="mini" @click="dialogFixVisible = true" :disabled='scope.row.num==null||scope.row.num==0'>维修</el-button>
          </template>
        </el-table-column>
      </el-table>
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
          label-width="80px"
        >
          <el-form-item
            label="设备编号"
            prop="chargeNumber"
          >
            <el-input v-model="formLabelEdit.chargeNumber"></el-input>
          </el-form-item>
          <el-form-item
            label="设备类型"
            prop="type"
          >
            <el-radio
              v-model="formLabelEdit.type"
              :label="1"
            >充电宝</el-radio>
            <el-radio
              v-model="formLabelEdit.type"
              :label="0"
            >充电柜</el-radio>
          </el-form-item>
          <el-form-item label="所在区域">
            <el-cascader
              size="large"
              :options="options2"
              v-model="shopAddress"
              @change="addressChange"
              :props="props"
            ></el-cascader>
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
        title="更换绑定设备"
        :visible.sync="dialogDeleteVisible"
        append-to-body
      >
        <el-form
          :model="formLabelAdd"
          :rules="rules"
          ref="formLabelAdd"
          label-position="right"
          label-width="80px"
        >

          <el-form-item label="店铺地址">
            <el-cascader
              size="large"
              :options="options2"
              v-model="shopAddress"
              @change="addressChange"
              :props="props"
            ></el-cascader>
          </el-form-item>
          <el-form-item
            label="设备编号"
          >
            <el-select
              v-model="formLabelAdd.newId"
              filterable
              remote
              reserve-keyword
              placeholder="请输入关键词"
              :filter-method="getChargesList"
            >
              <el-option
                v-for="item in options1"
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
          <el-button @click="dialogDeleteVisible = false">取 消</el-button>
          <el-button
            type="primary"
            @click="saveDelete('formLabelDelete')"
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
import {
  provinceAndCityData,
  regionData,
  provinceAndCityDataPlus,
  regionDataPlus,
  CodeToText,
  TextToCode
} from "element-china-area-data";
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
      dialogEditVisible: false,
      dialogAddVisible: false,
      dialogFixVisible: false,
      onlineDialogVisible: false,
      dialogDeleteVisible: false,
      userList: [],
      detailList: [],
      nameList: [],
      roleList: [],
      options: [],
      options1: [],
      options3: [],
      options4: [],
      shopAddress: [],
      props: {
        value: "label"
      },
      options2: provinceAndCityData,
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
      formLabelFix: {},
      formLabelDelete: {},
      rules: {
        maintenancePersonnelId: [
          { required: true, message: "请选择人员", trigger: "change" }
        ],
        maintainChargeId: [
          { required: true, message: "请选择设备", trigger: "change" }
        ],
        type: [
          { required: true, message: "请选择设备类型", trigger: "change" }
        ],
        chargeNumber: [
          { required: true, message: "请输入编号", trigger: "blur" }
        ]
      },
      save: true,
      details: true,
      scrap: true,
      replace: true
    };
  },
  mounted() {
    this.getUserList(this.formLabelSearch.page, this.formLabelSearch.rows);
    this.getBtnPms(this.$route.name);
    this.getChargeList2()
    //新增权限
    this.btnList.some(item => {
      return (this.save = item.power == "charge_save");
    });
    //编辑权限
    this.btnList.some(item => {
      return (this.details = item.power == "charge_details");
    });
    //报废权限
    this.btnList.some(item => {
      return (this.scrap = item.power == "charge_scrap");
    });
    //替换权限
    this.btnList.some(item => {
      return (this.replace = item.power == "charge_replace");
    });
  },
  methods: {
    // 获取设备编号
    getChargeList2(val) {
      this.$axios
        .post(
          "/management/charge/getChargeCombo",
          this.$qs.stringify({ page: 1, rows: 50, chargeNo: val ,type:0,status:1})
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.options3 = res.data.data.rows;
          }
        });
    },
    //获取列表数据
    getUserList(page, rows) {
      console.log(rows);
      this.$axios
        .post(
          "/management/charge/selectChargeList",
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
    //获取店铺列表
    getShopList(val) {
      this.$axios
        .post(
          "/management/shop/selectShopList",
          this.$qs.stringify({ page: 1, rows: 50, shopName: val })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            if (res.data.code == 200) {
              this.options = res.data.data.rows;
            } else {
              this.$message({
                type: "warning",
                message: `操作异常!`
              });
            }
          }
        });
    },
    //获取充电柜列表
    getChargesList(val) {
      this.$axios
        .post(
          "/management/charge/getChargeCombo",
          this.$qs.stringify({
            chargeNo: val,
            address: this.formLabelDelete.address,
            page: 1,
            rows: 50
          })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.options1 = res.data.data.rows;
          }
        });
    },
    // 状态筛选、
    filterTag(value) {
      this.formLabelSearch.status = Object.values(value)[0][0];
      this.getUserList(1, 10);
    },
    //dialog打开回调
    open() {
      this.formLabelAdd = {};
      this.shopAddress = [];
    },
    //新增
    handleAdd(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$axios
            .post(
              "/management/charge/saveCharge ",
              this.$qs.stringify(this.formLabelAdd)
            )
            .then(res => {
              if (res.status == 200) {
                if (res.data.code == 1) {
                  this.$message({
                    type: "warning",
                    message: `设备编号重复`
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
      this.formLabelEdit = {};
      this.shopAddress = [];
      console.log(rows);

      this.formLabelEdit = {
        id: rows.id,
        chargeNumber: rows.charge_number,
        type: rows.type,
        name: rows.name
      };
      if (rows.address) {
        this.shopAddress = rows.address.split(" ");
      }
      this.dialogEditVisible = true;
    },
    //确认编辑
    saveEdit(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$axios
            .post(
              "/management/charge/saveCharge",
              this.$qs.stringify(this.formLabelEdit)
            )
            .then(res => {
              if (res.status == 200) {
                console.log(res);
                if (res.data.code == 1) {
                  this.$message({
                    type: "warning",
                    message: `设备编号重复`
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
    //选择地址
    addressChange(arr) {
      console.log(arr);

      this.formLabelAdd.address = arr.join(" ");
      if (this.dialogEditVisible) {
        this.formLabelEdit.address = arr.join(" ");
      }
      if (this.dialogDeleteVisible) {
        this.formLabelDelete.address = arr.join(" ");
        this.getChargesList("");
      }
    },
    handleDelete(index, rows) {
      console.log(rows);
      this.idx = rows.id;
      this.$confirm("此操作将报废该设备, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        this.$axios.get(`/management/charge/scrap?id=${rows.id}`).then(res => {
          if (res.status == 200) {
            console.log(res);
            if (res.data.code == 0) {
              this.$message.success("报废成功");
              this.getUserList(1, 10);
            } else if (res.data.code == 1) {
              this.$message.warning("该设备已绑定门店");
              this.dialogDeleteVisible = true;
            } else if (res.data.code == 2) {
              this.$message.warning("未找到改设备");
            }
          }
        });
      });
    },
    // 更换设备编号后的确定删除
    saveDelete() {
      this.$axios
        .post(
          "/management/charge/replace",
          this.$qs.stringify({
            newId: this.formLabelAdd.newId,
            oldId: this.idx
          })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            if(res.data.code==0){
              this.$message.success("更新成功！");
            this.getUserList(
              this.formLabelSearch.page,
              this.formLabelSearch.rows
            );
            this.dialogDeleteVisible = false;
            }else{
              this.$message.error("未找到设备！");
            }
          }
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
    //导出数据
    handleExport() {},
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