<template>
  <div class="contain-box">
    <div class="table-box">
      <el-form label-position="right" label-width="80px" :model="formLabelSearch" :inline="true" @submit.native.prevent>
        <el-form-item label="用户名">
          <el-input placeholder="请输入名称" v-model="formLabelSearch.username" @keyup.enter.native="handleSearch"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleSearch">查询</el-button>
          <!-- <el-button @click="handleReset">重置</el-button> -->
        </el-form-item>
      </el-form>
    </div>
    <div class="table-box">
      <div class="function">
        <h2 v-if="sumDeposit">押金总额：{{total$}}</h2><br>
        <el-button type="primary" @click="handleExport" v-if="excel">批量导出</el-button>
      </div>
      <el-table :data="userList" border style="width: 100%"  @selection-change="handleSelectionChange"
        :row-key="getRowKeys">
        <el-table-column
          type="selection"
          width="55"
          align="center"
          reserve-selection
        ></el-table-column>
        <el-table-column prop="id" label="用户ID" align="center"></el-table-column>
        <el-table-column prop="realname" label="用户名" align="center"></el-table-column>
        <el-table-column prop="deposit" label="押金金额" align="center"></el-table-column>
        <el-table-column prop="amount" label="余额" align="center"></el-table-column>
        <!-- <el-table-column prop="allamount" label="总消费额度" align="center"> -->
        <!-- </el-table-column> -->
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
      total$:0,
      page: 1,
      rows: 1,
      idx: -1,
      state:-1,
      title:"",
      flag:true,
      loading: true,
      dataPick: "",
      dialogEditVisible: false,
      dialogAddVisible: false,
      onlineDialogVisible: false,
      userList: [],
      detailList: [],
      nameList: [],
      roleList: [],
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
      rules: {
        loginName: [
          { required: true, message: "请输入账号", trigger: "blur" }
        ],
        password: [
          { required: true, message: "请输入密码", trigger: "blur" }
        ],
        name: [{ required: true, message: "请输入姓名", trigger: "blur" }]
      },
      sumDeposit:true,
      excel:true,
    };
  },
  mounted() {
    this.getUserList(this.formLabelSearch.page, this.formLabelSearch.rows);
    //获取押金总和
    this.$axios.get('/management/wallet/sumDeposit').then(res=>{
      if(res.status==200){
        console.log(res)
        this.total$ = res.data.data
      }
    })
    this.getBtnPms(this.$route.name)
    //获取押金总额权限
    this.btnList.some(item=>{
      return this.sumDeposit = item.power == 'wallet_sumDeposit'
    })
    //导出权限
    this.btnList.some(item=>{
      return this.excel = item.power == 'wallet_excel'
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
          "/management/wallet/selectwalletList",
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
                if(res.data.code == 506){
                  this.$message({
                  type: "warning",
                  message: `账号重复`
                });
                return false
                }else if(res.data.code == 507){
                  this.$message({
                  type: "warning",
                  message: `手机号重复`
                });
                return false
                }
                this.$message({
                  type: "success",
                  message: `添加成功!`
                });
                this.dialogAddVisible = false;
                this.getUserList(1, 10);
                this.$refs[formName].resetFields();
                this.formLabelAdd={}
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
        id:rows.id,
        loginName: rows.loginName,
        password: rows.password,
        name: rows.name,
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
                console.log(res)
                if(res.data.code == 506){
                  this.$message({
                  type: "warning",
                  message: `账号重复`
                });
                return false
                }else if(res.data.code == 507){
                  this.$message({
                  type: "warning",
                  message: `手机号重复`
                });
                return false
                }
                this.$message({
                  type: "success",
                  message: `编辑成功!`
                });
                this.dialogEditVisible = false;
                this.getUserList(1, 10);
                this.$refs[formName].resetFields();
                this.formLabelEdit={}
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
    handleExport() {
      // window.location.href = `http://192.168.51.60:8401/management/wallet/createWalletExcel?username=${
      //   this.formLabelSearch.username?this.formLabelSearch.username:''}`;
        let ids = [];
      this.multipleSelection.filter(item => {
        ids.push(item.userId);
      });
      ids = ids.join(",");
      console.log(ids);
      if(ids==''){
        this.$message.error('请勾选需要导出的数据')
        return
      }
      this.$axios
        .post(`/management/wallet/createWalletExcel`,this.$qs.stringify({ids:ids}),{
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
            elink.download = "押金报表.xls";
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
.black{
  color: #fff;
  opacity: 0;
}
</style>