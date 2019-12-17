<template>
  <div class="contain-box">
    <!-- <div class="table-box">
      <el-form
        label-position="right"
        label-width="80px"
        :model="formLabelSearch"
        :inline="true"
        @submit.native.prevent
      >
        <el-form-item label="商家id">
          <el-input
            placeholder="请输入商家id"
            v-model="formLabelSearch.shopId"
            @keyup.enter.native="handleSearch"
          ></el-input>
        </el-form-item>
        <el-form-item>
          <el-button
            type="primary"
            @click="handleSearch"
          >查询</el-button>
        </el-form-item>
      </el-form>
    </div> -->
    <div class="table-box">
      <div class="function">
        <el-button
          type="primary"
          @click="dialogAddVisible = true"
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
            label-width="100px"
          >
            <el-form-item label="菜单名">
              <el-input v-model="formLabelAdd.name"></el-input> 
            </el-form-item>
            <el-form-item label="上级pid">
              <el-input v-model="formLabelAdd.pid" disabled></el-input>
            </el-form-item>
            <el-form-item label="地址">
              <el-input v-model="formLabelAdd.url"></el-input>
            </el-form-item>
            <el-form-item label="权限keyword">
              <el-input v-model="formLabelAdd.power"></el-input>
            </el-form-item>
            <el-form-item label="类型">
              <el-input v-model="formLabelAdd.type"></el-input>
            </el-form-item>
            <el-form-item label="排序">
              <el-input v-model="formLabelAdd.orderNum"></el-input>
            </el-form-item>
            <el-form-item label="备注">
              <el-input v-model="formLabelAdd.descritpion"></el-input>
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
          :visible.sync="dialogEditVisible"
          @open='open'
        >
          <el-form
            :model="formLabelEdit"
            :rules="rules"
            ref="formLabelEdit"
            label-position="right"
            label-width="100px"
          >
            <el-form-item label="菜单名">
              <el-input v-model="formLabelEdit.name"></el-input>
            </el-form-item>
            <el-form-item label="上级pid">
              <el-input v-model="formLabelEdit.pid" ></el-input>
            </el-form-item>
            <el-form-item label="地址">
              <el-input v-model="formLabelEdit.url"></el-input>
            </el-form-item>
            <el-form-item label="权限keyword">
              <el-input v-model="formLabelEdit.power"></el-input>
            </el-form-item>
            <el-form-item label="类型">
              <el-input v-model="formLabelEdit.type"></el-input>
            </el-form-item>
            <el-form-item label="排序">
              <el-input v-model="formLabelEdit.orderNum"></el-input>
            </el-form-item>
            <el-form-item label="备注">
              <el-input v-model="formLabelEdit.descritpion"></el-input>
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
        @current-change="handleCurrentChange"
        :row-key="getid"
        highlight-current-row
        :expand-row-keys="expand"
      >
        <el-table-column
          prop="name"
          label="菜单名"
          align="center"
          show-overflow-tooltip
        ></el-table-column>
        <el-table-column
          prop="type"
          label="类型"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="descritpion"
          label="描述"
          align="center"
        >
        </el-table-column>
        <el-table-column
          label="操作"
          align="center"
        >
          <template slot-scope="scope">
            <el-button
              size="mini"
              @click="handleEdit(scope.$index, scope.row)"
            >编辑</el-button>
            <el-button
              size="mini"
              @click="handleDelete(scope.$index, scope.row)"
            >删除</el-button>
          </template>
        </el-table-column>
      </el-table>
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
      idx: -1,
      state: -1,
      title: "",
      flag: true,
      loading: true,
      dataPick: "",
      dialogEditVisible: false,
      dialogAddVisible: false,
      onlineDialogVisible: false,
      userList: [],
      detailList: [],
      nameList: [],
      roleList: [],
      options: [],
      options1: [],
      shopAddress: [],
      expand: [],
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
      rules: {
        chargeNumber: [
          { required: true, message: "请输入编号", trigger: "blur" }
        ]
      }
    };
  },
  computed: {
    numAdd() {
      return this.formLabelAdd.facilityNo;
    }
  },
  watch: {
    numAdd(val) {
      this.formLabelAdd.num = val.length;
    }
  },
  mounted() {
    this.getUserList(this.formLabelSearch.page, this.formLabelSearch.rows);
    this.expand.push('1');
  },
  methods: {
    getid(row){
      return row.id
    },
    //获取列表数据
    getUserList(page, rows) {
      this.$axios
        .post(
          "/management/permisson/getTreeData",
          this.$qs.stringify(this.formLabelSearch)
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            if (res.data.code == 200) {
              this.userList = res.data.data;
            } else {
              this.$message({
                type: "warning",
                message: `操作异常!`
              });
            }
          }
        });
    },
    // 选中行
    handleCurrentChange(val) {
        console.log(val)
        if(val){
          this.formLabelAdd.pid = val.id
        }
      },
    //dialog打开回调
    open() {
    //   this.formLabelAdd = {};
    //   this.shopAddress = [];
    },
    //新增
    handleAdd(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          // this.fo
          this.$axios
            .post(
              "/management/permisson/save",
              this.$qs.stringify(this.formLabelAdd)
            )
            .then(res => {
              if (res.status == 200) {
                if (res.data.code == 1) {
                  this.$message({
                    type: "warning",
                    message: `权限关键词重复`
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
      console.log(rows)
        this.idx =rows.id 
        this.dialogEditVisible = true
        this.$axios.get(`/management/permisson/getDetails?id=${rows.id}`).then(res=>{
            if(res.status==200){
                this.formLabelEdit =res.data.data
            }
        })
    },
    // 保存编辑
    saveEdit(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          // this.fo
          this.$axios
            .post(
              `/management/permisson/save?id=${this.idx}`,
              this.$qs.stringify(this.formLabelEdit)
            )
            .then(res => {
              if (res.status == 200) {
                if (res.data.code == 1) {
                  this.$message({
                    type: "warning",
                    message: `权限关键词重复`
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
    //选择地址
    addressChange(arr) {
      console.log(arr);

      this.formLabelAdd.address = arr.join(" ");
      if (this.dialogEditVisible) {
        this.formLabelEdit.address = arr.join(" ");
      }
    },
    handleDelete(index, rows) {
      console.log(rows);
      this.$confirm("此操作将永久删除该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        this.$axios
          .get(`/management/permisson/delete?id=${rows.id}`)
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
    handleExport() {},
    //分页
    handleSizeChange(val) {
      this.formLabelSearch.rows = val;
      this.getUserList(this.formLabelSearch.page, this.formLabelSearch.rows);
    },
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
.table-box{
    padding-bottom: 20px
}
</style>