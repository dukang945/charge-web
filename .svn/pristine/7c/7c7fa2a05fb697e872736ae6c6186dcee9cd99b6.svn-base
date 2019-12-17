<template>
  <div class="contain-box">
  
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
            label-width="80px"
          >
            <el-form-item label="角色名" prop="cnName">
              <el-input v-model="formLabelAdd.cnName"></el-input>
            </el-form-item>
            <el-form-item label="英文名" prop="enName">
              <el-input v-model="formLabelAdd.enName"></el-input>
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
            label-width="80px"
          >
            <el-form-item label="角色名" prop="cnName">
              <el-input v-model="formLabelEdit.cnName"></el-input>
            </el-form-item>
            <el-form-item label="英文名" prop="enName">
              <el-input v-model="formLabelEdit.enName"></el-input>
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
        <el-dialog title="角色分配资源" :visible.sync="dialogVisible" width="30%"
          @open='open'>
            <el-form label-position="left" label-width="80px" :model="formLabelAllot">
              <el-tree
                :data="partTree"
                :props="defaultProps"
                @check="getCheckedKeys"
                show-checkbox
                node-key="id"
                ref="tree"
                highlight-current
                default-expand-all
                check-strictly
                :default-checked-keys="checkList"
              ></el-tree>
            </el-form>
            <span slot="footer" class="dialog-footer">
              <el-button @click="dialogVisible=false">取 消</el-button>
              <el-button type="primary" @click="savePartEdit">确 定</el-button>
            </span>
          </el-dialog>
      </div>
      <el-table
        :data="userList"
        border
        style="width: 100%"
        @filter-change="filterTag"
      >
        <el-table-column
          prop="cnName"
          label="角色名"
          align="center"
          show-overflow-tooltip
        >
        <template slot-scope="scope">
          <span v-if="scope.row.status==0">{{scope.row.cnName}}</span>
          <span v-if="scope.row.status==2" style="text-decoration:line-through;color:#f56c6c">{{scope.row.cnName}}</span>
        </template>
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
              @click="handleDistribution(scope.$index, scope.row)"
            >分配权限</el-button>
            <el-button
              size="mini"
              @click="handleDelete(scope.$index, scope.row)"
              :disabled="scope.row.status==2"
            >删除</el-button>
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
      dialogVisible: false,
      userList: [],
      detailList: [],
      nameList: [],
      roleList: [],
      options: [],
      options1: [],
      shopAddress: [],
      partTree: [],
      checkList: [],
      props: {
        value: "label"
      },
      defaultProps: {
        children: "children",
        label: "name"
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
      formLabelAllot: {},
      rules: {
        enName: [
          { required: true, message: "请输入完整信息", trigger: "blur" },
        ],
        cnName: [
          { required: true, message: "请输入完整信息", trigger: "blur" },
        ],
      }
    };
  },
  mounted() {
    this.getUserList(this.formLabelSearch.page, this.formLabelSearch.rows);
    this.getShopList()
    this.getBtnPms(this.$route.name)
  },
  methods: {
    //获取列表数据
    getUserList(page, rows) {
      console.log(rows);
      this.$axios
        .post(
          "/management/role/selectList",
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
    //获取树结构
    getShopList() {
      this.$axios.get('/management/permisson/getTreeData').then(res=>{
        console.log(res)
        if(res.status==200){
          this.partTree =res.data.data
        }
      })
    },
    // 状态筛选、
    filterTag(value) {
      this.formLabelSearch.state = Object.values(value)[0][0];
      this.getUserList(1, 10);
    },
    //dialog打开回调
    open() {
      this.formLabelAdd = {};
      this.shopAddress = [];
      if(this.$refs.tree){
        this.$refs.tree.setCheckedKeys([]);
      }
    },
    //新增
    handleAdd(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$axios
            .post(
              "/management/role/saveRole",
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
      this.idx = rows.id;
      this.dialogEditVisible = true;
      this.$axios
        .get(`/management/role/getSysRoleDetails?id=${this.idx}`)
        .then(res => {
          this.formLabelEdit = res.data.data;
        });
    },
    saveEdit() {
      this.$axios
        .post(
          `/management/role/saveRole?id=${this.idx}`,
          this.$qs.stringify(this.formLabelEdit)
        )
        .then(res => {
          if (res.status == 200) {
            this.$message.success("修改成功！");
            this.formLabelEdit = {};
            this.dialogEditVisible = false;
          }
        });
    },
    // 分配权限
    getCheckedKeys(leafOnly, key) {
      this.checkList = key.checkedKeys;
    },
    handleDistribution(index,rows){
      if(rows.enName != 'finance'){
        this.partTree[2].children[2].disabled = true
        this.partTree[2].children[2].children[0].disabled = true
        this.partTree[2].children[2].children[1].disabled = true
        this.partTree[2].children[2].children[2].disabled = true
      }else{
        this.partTree[2].children[2].disabled = false
        this.partTree[2].children[2].children[0].disabled = false
        this.partTree[2].children[2].children[1].disabled = false
        this.partTree[2].children[2].children[2].disabled = false
      }
      this.idx =rows.id
      this.$axios.get(`/management/permisson/getIdListByRoleId?roleId=${this.idx}`).then(res=>{
        if(res.status==200){
          console.log(res)
          this.checkList =res.data.data
      this.dialogVisible =true
          
        }
      })
    },
    savePartEdit(){
      console.log(this.checkList);
      this.$axios
        .post(
          "/management/role/setRolePermission",
          this.$qs.stringify({
            roleId: this.idx,
            permissions: this.checkList.join()
          })
        )
        .then(res => {
          if (res.status == 200) {
            this.dialogVisible = false;
            this.$message.success(`修改成功`);
          }
        });
      this.checkList = [];
      this.$refs.tree.setCheckedKeys([]);
    },
    handleDelete(index, rows) {
      console.log(rows);
      this.$confirm("此操作将永久删除该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        this.$axios
          .get(`/management/role/delete?id=${rows.id}&type=0`)
          .then(res => {
            if (res.status == 200) {
              console.log(res);
              if (res.data.code) {
                this.$confirm("该角色下已有用户，确认删除吗？", "提示", {
                  confirmButtonText: "确定",
                  cancelButtonText: "取消",
                  type: "warning"
                }).then(()=>{
                  this.$axios.get(`/management/role/delete?id=${rows.id}&type=1`).then(res=>{
                    if(res.status==200){
                      this.$message.success('删除成功!')
                      this.getUserList(1,10)
                    }
                  })
                });
              }else{
                this.$message.success('删除成功!')
                this.getUserList(1,10)
              }
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