<template>
  <div class="contain-box">
    <div class="table-box">
      <el-form
        label-position="right"
        label-width="100px"
        :model="formLabelSearch"
        :inline="true"
        @submit.native.prevent
      >
        <el-form-item label="维修人员姓名">
          <el-input placeholder="请输入姓名" v-model="formLabelSearch.searchName" @keyup.enter.native="handleSearch"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleSearch">查询</el-button>
        </el-form-item>
      </el-form>
    </div>
    <div class="table-box">
      <div class="function">
        <el-button type="primary" @click="dialogAddVisible = true" v-if="save">新增</el-button>
        <el-button type="primary" @click="handleDelete" v-if="save">批量删除</el-button>
        <el-dialog title="新增" :visible.sync="dialogAddVisible" @open="open">
          <el-form
            :model="formLabelAdd"
            :rules="rules"
            ref="formLabelAdd"
            label-position="right"
            label-width="80px"
          >
            <el-form-item label="姓名" prop="name">
              <el-input v-model="formLabelAdd.name"></el-input>
            </el-form-item>
            <el-form-item label="维修区域">
              <el-cascader
                size="large"
                :options="options"
                v-model="shopAddress"
                @change="addressChange"
                :props="props"
              ></el-cascader>
            </el-form-item>
            <el-form-item label="区域说明">
              <el-input v-model="formLabelAdd.areaDescription" placeholder="请输入详细地址"></el-input>
            </el-form-item>
            <el-form-item label="电话号码" prop="tel">
              <el-input v-model="formLabelAdd.tel" placeholder="请输入手机号"></el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogAddVisible = false">取 消</el-button>
            <el-button type="primary" @click="handleAdd('formLabelAdd')">确 定</el-button>
          </div>
        </el-dialog>
      </div>
      <el-table :data="userList" border style="width: 100%" @selection-change="handleSelectionChange"
        :row-key="getRowKeys">
        <el-table-column
          type="selection"
          width="55"
          align="center"
          reserve-selection
        ></el-table-column>
        <el-table-column prop="id" label="id" align="center"></el-table-column>
        <el-table-column prop="name" label="姓名" align="center"></el-table-column>
        <el-table-column prop="maintenanceArea" label="维修区域" align="center"></el-table-column>
        <el-table-column label="操作" align="center" width="300">
          <template slot-scope="scope">
            <el-button size="mini" @click="handleDetail(scope.$index, scope.row)" v-if="selectOrderById">已维修订单</el-button>
            <el-button size="mini" @click="handleEdit(scope.$index, scope.row)" v-if="details">编辑</el-button>
            <el-button size="mini" @click="handleDelete(scope.$index, scope.row)" v-if="del">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-dialog title="编辑" :visible.sync="dialogEditVisible" append-to-body @open="open">
        <el-form
          :model="formLabelEdit"
          :rules="rules"
          ref="formLabelEdit"
          label-position="right"
          label-width="80px"
        >
          <el-form-item label="姓名" prop="name">
            <el-input v-model="formLabelEdit.name"></el-input>
          </el-form-item>
          <el-form-item label="维修区域">
            <el-cascader
              size="large"
              :options="options"
              v-model="shopAddress"
              @change="addressChange"
              :props="props"
            ></el-cascader>
          </el-form-item>
          <el-form-item label="区域说明">
            <el-input v-model="formLabelEdit.areaDescription" placeholder="请输入详细地址"></el-input>
          </el-form-item>
          <el-form-item label="电话号码" prop="tel">
            <el-input v-model="formLabelEdit.tel" placeholder="请输入手机号"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogEditVisible = false">取 消</el-button>
          <el-button type="primary" @click="saveEdit('formLabelEdit')">确 定</el-button>
        </div>
      </el-dialog>
      <el-dialog title="编辑" :visible.sync="dialogDetailVisible" append-to-body @open="open">
          <el-table :data="gridData">
    <el-table-column property="order_number" label="订单编号"></el-table-column>
    <el-table-column property="charge_number" label="设备号"></el-table-column>
    <el-table-column property="order_status" label="订单状态">
      <template slot-scope="scope">
        <span v-if="scope.row.order_status==0">已完成</span>
        <span v-if="scope.row.order_status==1">维修中</span>
        <span v-if="scope.row.order_status==2">已取消</span>
      </template>
    </el-table-column>
  </el-table>
  <el-pagination
        @size-change="handleSizeChange1"
        @current-change="handleCurrentChange1"
        :page-sizes="[10, 20]"
        :current-page="page"
        :page-size="10"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total1"
      ></el-pagination>
      </el-dialog>
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :page-sizes="[10, 20]"
        :current-page="1"
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
import OSS from "ali-oss";
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
      page: 1,
      rows: 10,
      idx: -1,
      state: -1,
      title: "",
      flag: true,
      loading: true,
      options: regionData,
      shopAddress: [],
      dataPick: "",
      dialogEditVisible: false,
      dialogAddVisible: false,
      dialogDetailVisible: false,
      userList: [],
      detailList: [],
      nameList: [],
      roleList: [],
      gridData: [],
      multipleSelection: [],
      getRowKeys(row) {
        return row.id;
      },
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
      rules: {
        loginName: [{ required: true, message: "请输入账号", trigger: "blur" }],
        tel:[
          { required: true, validator: validatePass3, trigger: "blur" }
        ],
        name: [{ required: true, message: "请输入姓名", trigger: "blur" }]
      },
      save:true,
      details:true,
      del:true,
      selectOrderById:true,
    };
  },
  mounted() {
    this.getUserList(this.formLabelAlign.page, this.formLabelAlign.rows);
    this.getBtnPms(this.$route.name)
    //新增权限
    this.btnList.some(item=>{
      return this.save = item.power == 'maintenancePersonnel_save'
    })
    //编辑权限
    this.btnList.some(item=>{
      return this.details = item.power == 'maintenancePersonnel_details'
    })
    //删除权限
    this.btnList.some(item=>{
      return this.del = item.power == 'maintenancePersonnel_delete'
    })
    //查看订单权限
    this.btnList.some(item=>{
      return this.selectOrderById = item.power == 'maintenancePersonnel_selectOrderById'
    })
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
          "/management/maintenancePersonnel/selectList",
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
    // 获取订单详情列表
    getDetailList(page,rows){
        this.$axios.post(`/management/maintenanceOrder/selectList?maintenancePersonnelId=${this.idx}`,this.$qs.stringify({page:page,rows:rows})).then(res=>{
            if(res.status==200){
                console.log(res)
                this.gridData = res.data.data.rows
                this.total1 = res.data.data.total
            }
        })
    },
    //选择地址
    addressChange(arr) {
      console.log(arr);
      this.formLabelAdd.maintenanceArea = arr.join(" ");
      if (this.dialogEditVisible) {
        this.formLabelEdit.maintenanceArea = arr.join(" ");
      }
    },
    // dialog 打开前的回调
    open() {
      this.shopAddress = [];
      this.formLabelAdd = this.formLabelEdit = {};
      if(this.$refs['formLabelAdd']){
        this.$refs['formLabelAdd'].resetFields();
      }
    },
    //新增
    handleAdd(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$axios
            .post(
              "/management/maintenancePersonnel/save",
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
    // 查看订单
    handleDetail(index, rows) {
        this.dialogDetailVisible =true
        this.idx = rows.id
        this.page = 1
        this.getDetailList(1,10)
        
    },
    //编辑
    handleEdit(index, rows) {
      this.formLabelEdit = {};
      console.log(rows);
      this.idx = rows.id;
      this.dialogEditVisible = true;
      this.$axios
        .post(`/management/maintenancePersonnel/selectDetails?id=${rows.id}`)
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            if(res.data.code==200){
              this.formLabelEdit = {
              id: res.data.data.id,
              name: res.data.data.name,
              tel: res.data.data.tel,
              areaDescription: res.data.data.areaDescription
            };
            this.shopAddress = res.data.data.maintenanceArea.split(" ");
            }else{
              this.$message.error('请求数据失败')
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
              `/management/maintenancePersonnel/save?id=${this.idx}`,
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
      let ids = [];
      this.multipleSelection.filter(item => {
        ids.push(item.id);
      });
      ids = ids.join(",");
      console.log(ids);
      if(ids==''&& !rows){
        this.$message.error('请勾选需要删除的数据')
        return
      }
      this.$confirm("此操作将永久删除该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        this.$axios
          .post(`/management/maintenancePersonnel/delete?ids=${rows?rows.id:ids}`)
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
      window.location.href = `http://192.168.12.11:8401/management/sysAccount/exportExcel?phone=${
        this.formLabelAlign.phone
      }&loginAccount=${this.formLabelAlign.loginAccount}&roleName=${
        this.formLabelAlign.roleName
      }&state=${this.formLabelAlign.state}`;
    },
    //分页
    handleSizeChange(val) {
      this.formLabelAlign.rows = val;
      this.getUserList(this.formLabelAlign.page, this.formLabelAlign.rows);
    },
    handleCurrentChange(val) {
      this.formLabelAlign.page = val;
      this.getUserList(this.formLabelAlign.page, this.formLabelAlign.rows);
    },
    handleSizeChange1(val) {
      this.rows = val;
      this.getDetailList(this.page, this.rows);
    },
    handleCurrentChange1(val) {
      this.page = val;
      this.getDetailList(this.page, this.rows);
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
</style>