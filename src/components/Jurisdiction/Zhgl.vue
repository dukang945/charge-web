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
        <el-form-item label="账号">
          <el-input
            placeholder="请输入账号"
            v-model="formLabelSearch.loginName"
            @keyup.enter.native="handleSearch"
          ></el-input>
        </el-form-item>
        <el-form-item label="角色">
          <el-select
            v-model="formLabelSearch.roleId"
            placeholder="请选择角色"
          >
            <el-option
              label="全部"
              value=""
            ></el-option>
            <el-option
              v-for="item in roleList"
                  :key="item.id"
                  :label="item.text"
                  :value="item.value"
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
            label-width="100px"
          >
            <el-form-item
              label="姓名"
              prop="name"
            >
              <el-select
                v-model="formLabelAdd.name"
                filterable
                allow-create
                placeholder="请选择"
                :filter-method='getNameList'
              >
                <el-option
                  v-for="item in nameList"
                  :key="item.id"
                  :label="item.name"
                  :value="item.name"
                >
                </el-option>
              </el-select>
            </el-form-item>
            <el-form-item
              label="账号(手机)"
              prop="loginName"
            >
              <el-input v-model.number="formLabelAdd.loginName"></el-input>
            </el-form-item>
            <el-form-item
              label="密码"
              prop="password"
            >
              <el-input
                v-model="formLabelAdd.password"
                show-password
                maxlength="10"
                minlength="6"
                show-word-limit
              ></el-input>
            </el-form-item>
            <el-form-item
              label="角色"
              prop="roleIds"
            >
              <el-select
                v-model="formLabelAdd.roleIds"
                filterable
                :filter-method="getShopList"
                placeholder="请选择"
              >
                <el-option
                  v-for="item in roleList"
                  :key="item.value"
                  :label="item.text"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
            <div v-if="formLabelAdd.roleIds == 2">
              <el-form-item label="工号" prop="jobNumber">
                <el-input v-model="formLabelAdd.jobNumber"></el-input>
              </el-form-item>
              <el-form-item label="城市" prop="city">
                <el-input v-model="formLabelAdd.city"></el-input>
              </el-form-item>
              <el-form-item label="分成比例" prop="divideRatio">
                <el-input
                  v-model="formLabelAdd.divideRatio"
                  type='number'
                  placeholder="请填写分成百分比"
                >
                  <template slot="append">%</template>
                </el-input>
              </el-form-item>
              <el-form-item label="月度指标" prop="monthPerformance">
                <el-input v-model="formLabelAdd.monthPerformance"></el-input>
              </el-form-item>
            </div>
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
      </div>
      <el-dialog
        title="修改密码"
        :visible.sync="dialogResetVisible"
        @open="open"
      >
        <el-form
          :model="formLabelReset"
          :rules="rules"
          ref="formLabelReset"
          label-position="right"
          label-width="110px"
        >
          <el-form-item
            label="旧密码"
            prop="oldPassword"
          >
            <el-input
              v-model="formLabelReset.oldPassword"
              maxlength="10"
              minlength="6"
              show-word-limit
            ></el-input>
          </el-form-item>
          <el-form-item
            label="新密码"
            prop="newPassword"
          >
            <el-input
              v-model="formLabelReset.newPassword"
              show-password
              maxlength="10"
              minlength="6"
              show-word-limit
            ></el-input>
          </el-form-item>
          <el-form-item
            label="重新输入新密码"
            prop="check"
          >
            <el-input
              v-model="formLabelReset.check"
              show-password
            ></el-input>
          </el-form-item>
        </el-form>
        <div
          slot="footer"
          class="dialog-footer"
        >
          <el-button @click="dialogResetVisible = false">取 消</el-button>
          <el-button
            type="primary"
            @click="savePass('formLabelReset')"
          >确 定</el-button>
        </div>
      </el-dialog>
      <el-table
        :data="userList"
        border
        style="width: 100%"
        @filter-change="filterTag"
      >
        <el-table-column
          prop="NAME"
          label="姓名"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="loginname"
          label="账号"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="rolename"
          label="角色"
          align="center"
        ></el-table-column>
        <el-table-column
          label="操作"
          align="center"
          width="250"
        >
          <template slot-scope="scope">
            <!-- <el-button size="mini" @click="handleChange(scope.$index, scope.row)" v-if="changePassword">更改密码</el-button> -->
            <el-button
              size="mini"
              @click="handleReset(scope.$index, scope.row)"
              v-if="changePassword"
            >重置密码</el-button>
            <el-button
              size="mini"
              @click="handleEdit(scope.$index, scope.row)"
              v-if="details"
            >编辑</el-button>
            <el-button
              size="mini"
              @click="handleDelete(scope.$index, scope.row)"
              v-if="del"
            >删除</el-button>
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
          label-width="100px"
        >
          <el-form-item
            label="姓名"
            prop="name"
          >
            <el-input v-model="formLabelEdit.name"></el-input>
          </el-form-item>
          <el-form-item
            label="账号(手机)"
            prop="loginName"
          >
            <el-input v-model.number="formLabelEdit.loginName"></el-input>
          </el-form-item>
          <el-form-item
            label="密码"
            prop="password"
          >
            <el-input
              v-model="formLabelEdit.password"
              show-password
              maxlength="10"
              minlength="6"
              show-word-limit
            ></el-input>
          </el-form-item>
          <el-form-item
            label="角色"
            prop="roleIds"
          >
            <el-select
              v-model="formLabelEdit.roleIds"
              filterable
              :filter-method="getShopList"
              placeholder="请选择"
            >
              <el-option
                v-for="item in roleList"
                :key="item.value"
                :label="item.text"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
          <div v-if="formLabelEdit.roleIds == 2">
            <el-form-item label="工号" prop="jobNumber">
              <el-input v-model="formLabelEdit.jobNumber"></el-input>
            </el-form-item>
            <el-form-item label="城市" prop="city">
              <el-input v-model="formLabelEdit.city"></el-input>
            </el-form-item>
            <el-form-item label="分成比例" prop="divideRatio">
              <el-input
                v-model="formLabelEdit.divideRatio"
                type='number'
                placeholder="请填写分成百分比"
              >
                <template slot="append">%</template>
              </el-input>
            </el-form-item>
            <el-form-item label="月度指标" prop="monthPerformance">
              <el-input v-model="formLabelEdit.monthPerformance"></el-input>
            </el-form-item>
          </div>
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
    var validatePass1 = (rule, value, callback) => {
      console.log(value);
      var regs = /(?:\d.*_)|(?:_.*\d)|(?:[A-Za-z].*_)|(?:_.*[A-Za-z])|(?:[A-Za-z].*\d)|(?:\d.*[A-Za-z])/;
      if (!value) {
        callback(new Error("请输入密码"));
      } else if (!regs.test(value)) {
        callback(new Error("请输入字母数字结合的6位数密码!"));
      } else if (value.length < 6) {
        callback(new Error("最少输入6位数!"));
      } else {
        callback();
      }
    };
    var validatePass2 = (rule, value, callback) => {
      console.log(value);
      if (!value) {
        callback(new Error("请再次输入密码"));
      } else if (value !== this.formLabelReset.newPassword) {
        callback(new Error("两次输入密码不一致!"));
      } else {
        callback();
      }
    };
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
      onlineDialogVisible: false,
      dialogResetVisible: false,
      userList: [],
      nameList: [],
      detailList: [],
      nameList: [],
      roleList: [],
      filterList: [],
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
      formLabelReset: {},
      rules: {
        loginName: [
          { required: true, validator: validatePass3, trigger: "blur" }
        ],
        password: [
          { required: true, validator: validatePass1, trigger: "blur" }
        ],
        oldPassword: [
          { required: true, validator: validatePass1, trigger: "blur" }
        ],
        newPassword: [
          { required: true, message: "请输入密码", trigger: "blur" }
        ],
        jobNumber: [
          { required: true, message: "请输入工号", trigger: "blur" }
        ],
        city: [
          { required: true, message: "请输入城市", trigger: "blur" }
        ],
        divideRatio: [
          { required: true, message: "请输入分成比例", trigger: "blur" }
        ],
        newPasswomonthPerformancerd: [
          { required: true, message: "请输入指标", trigger: "blur" }
        ],
        name: [{ required: true, message: "请输入姓名", trigger: "blur" }],
        roleIds: [{ required: true, message: "请选择角色", trigger: "change" }],
        check: [{ validator: validatePass2, trigger: "blur" }]
      },
      save: true,
      changePassword: true,
      details: true,
      del: true
    };
  },
  mounted() {
    this.getUserList(this.formLabelSearch.page, this.formLabelSearch.rows);
    this.getShopList("");
    this.getBtnPms(this.$route.name);
    //新增权限
    this.btnList.some(item => {
      return (this.save = item.power == "sysUser_save");
    });
    //更改密码权限
    this.btnList.some(item => {
      return (this.changePassword = item.power == "sysUser_changePassword");
    });
    //编辑权限
    this.btnList.some(item => {
      return (this.details = item.power == "sysUser_details");
    });
    //删除权限
    this.btnList.some(item => {
      return (this.del = item.power == "sysUser_delete");
    });
  },
  computed: {
   Name() {
      return this.formLabelAdd.name;
    },
  NameEdit() {
      return this.formLabelEdit.name;
    }
  },
  watch: {
    Name(val) {
      this.nameList.filter(item => {
        if (item.name == val) {
          console.log(item);
          this.formLabelAdd.loginName = item.phone;
          this.formLabelAdd.city = item.shopAddrLoc
          this.formLabelAdd.sxypId = item.id
        }
      });
    },
    NameEdit(val) {
      this.nameList.filter(item => {
        if (item.name == val) {
          console.log(item);
          this.formLabelEdit.loginName = item.phone;
          this.formLabelEdit.city = item.shopAddrLoc
          this.formLabelEdit.sxypId = item.id
        }
      });
    }
  },
  methods: {
    //获取列表数据
    getUserList(page, rows) {
      console.log(rows);
      this.$axios
        .post(
          "/management/sysUser/selectList",
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
    //获取角色列表
    getShopList(val) {
      this.$axios
        .post(
          "/management/role/selectValid",
          this.$qs.stringify({
            text: val,
            page: 1,
            rows: 50
          })
        )
        .then(res => {
          if (res.status == 200) {
            this.roleList = res.data.data;
          }
        });
    },
    // 状态筛选、
    filterTag(value) {
      this.formLabelSearch.roleId = Object.values(value)[0][0];
      this.getUserList(1, 10);
    },
    // 获取用户列表
    getNameList(val) {
      this.$axios
        .post(
          "/management/salesman/getSalesmanByNameToSh",
          this.$qs.stringify({ name: val })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(JSON.parse(res.data.data));
            this.nameList = JSON.parse(res.data.data);
          }
        });
    },
    // dilog打开回调
    open() {
      this.formLabelAdd = {};
      if (this.$refs["formLabelAdd"]) {
        this.$refs["formLabelAdd"].resetFields();
      }
      if (this.$refs["formLabelReset"]) {
        this.$refs["formLabelReset"].resetFields();
      }
    },
    //新增
    handleAdd(formName) {
      var str = this.formLabelAdd.loginName;
      var reg = this.$refs[formName].validate(valid => {
        if (valid) {
          this.$axios
            .post(
              "/management/sysUser/saveSysUser",
              this.$qs.stringify(this.formLabelAdd)
            )
            .then(res => {
              if (res.status == 200) {
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
      console.log(rows);
      this.idx = rows.id;
      this.dialogEditVisible = true;
      this.$axios
        .get(`/management/sysUser/getSysUserDetails?id=${rows.id}`)
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.formLabelEdit = {
              id: res.data.data.id,
              loginName: res.data.data.loginName,
              name: res.data.data.name,
              password: res.data.data.password,
              roleIds: res.data.data.roles[0] ? res.data.data.roles[0].id : ""
            };
            if (res.data.data.roles[0].id == 2) {
              this.formLabelEdit = {
                loginName: res.data.data.loginName,
                name: res.data.data.name,
                password: res.data.data.password,
                roleIds: res.data.data.roles[0].id,
                city: res.data.data.salesmanDetails
                  ? res.data.data.salesmanDetails.city
                  : "",
                divideRatio: res.data.data.salesmanDetails
                  ? res.data.data.salesmanDetails.divideRatio
                  : "",
                jobNumber: res.data.data.salesmanDetails
                  ? res.data.data.salesmanDetails.jobNumber
                  : "",
                monthPerformance: res.data.data.salesmanDetails
                  ? res.data.data.salesmanDetails.monthPerformance
                  : ""
              };
            }
          }
        });
    },
    //确认编辑
    saveEdit(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$axios
            .post(
              `/management/sysUser/saveSysUser?id=${this.idx}`,
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
    // 修改密码
    handleChange(index, rows) {
      this.dialogResetVisible = true;
      this.formLabelReset = {};
      console.log(rows);
      this.idx = rows.id;
    },
    // 确认修改
    savePass(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$axios
            .post(
              "/management/sysUser/changePassword",
              this.$qs.stringify({
                userId: this.idx,
                oldPassword: this.formLabelReset.oldPassword,
                newPassword: this.formLabelReset.newPassword
              })
            )
            .then(res => {
              if (res.status == 200) {
                if (res.data.code == 1) {
                  this.$message({
                    type: "warning",
                    message: `旧密码错误`
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
                  message: `修改成功!`
                });
                this.dialogResetVisible = false;
                this.getUserList(1, 10);
                this.$refs[formName].resetFields();
                this.formLabelReset = {};
              }
            });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    // 重置密码
    handleReset(index, rows) {
      this.$confirm("此操作将恢复密码为默认值, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        this.$axios
          .get(`/management/sysUser/resetPasswords?userId=${rows.id}`)
          .then(res => {
            if (res.status == 200) {
              this.$message.success("重置成功");
              this.getUserList(1, 10);
            }
          });
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