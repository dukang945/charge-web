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
        <el-form-item label="用户名">
          <el-input
            placeholder="请输入"
            v-model="formLabelSearch.searchName"
            @keyup.enter.native="handleSearch"
          ></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleSearch">查询</el-button>
        </el-form-item>
      </el-form>
    </div>
    <div class="table-box">
      <div class="function">
        <el-button type="primary" @click="dialogAddVisible = true" v-if="save">新增</el-button>
        <el-button type="primary" @click="handleExport" v-if="createExcel">批量导出</el-button>
        <el-dialog title="新增" :visible.sync="dialogAddVisible" @open="open">
          <el-form
            :model="formLabelAdd"
            :rules="rules"
            ref="formLabelAdd"
            label-position="right"
            label-width="80px"
          >
            <el-form-item label="评论用户" prop="userId">
              <el-select
                v-model="formLabelAdd.userId"
                filterable
                :filter-method="getChargeList"
                placeholder="请选择"
              >
                <el-option
                  v-for="item in options1"
                  :key="item.id"
                  :label="item.realName"
                  :value="item.id"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="反馈内容" prop="message">
              <el-input
                type="textarea"
                :rows="2"
                placeholder="请输入内容"
                v-model="formLabelAdd.message"
              ></el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogAddVisible = false">取 消</el-button>
            <el-button type="primary" @click="handleAdd('formLabelAdd')">确 定</el-button>
            <el-button type="primary" @click="handleAdd('formLabelAdd',3)">不处理</el-button>
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
        <!-- <el-table-column prop="id" label="id" align="center"></el-table-column> -->
        <el-table-column prop="real_name" label="用户名" align="center"></el-table-column>
        <el-table-column prop="phone" label="手机号" align="center"></el-table-column>
        <el-table-column prop="message" label="反馈内容" align="center"></el-table-column>
        <el-table-column prop="create_time" label="反馈时间" align="center"></el-table-column>
        <el-table-column prop="create_time" label="反馈状态" align="center">
             <template slot-scope="scope">
                 <span v-if="scope.row.status==0" style="color:#E6A23C">待处理</span>
                 <span v-if="scope.row.status==1" style="color:#E6A23C">处理中</span>
            <span v-if="scope.row.status==2" style="color:#67C23A">已完成</span>
            <span v-if="scope.row.status==3" style="color:#F56C6C">已忽略</span>
             </template>
        </el-table-column>
        <el-table-column label="操作" align="center" width="300">
          <template slot-scope="scope">
            <el-button size="mini" @click="handleEdit(scope.$index, scope.row)" :disabled="scope.row.status ===2">编辑</el-button>
            <el-button size="mini" @click="handleCheck(scope.$index, scope.row)">查看订单</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-dialog title="编辑" :visible.sync="dialogEditVisible" append-to-body @open="open">
        <el-form
          :model="formLabelEdit"
          label-position="right"
          label-width="80px"
        >
          <el-form-item label="反馈内容">
            <el-input
              type="textarea"
              :rows="2"
              placeholder="请输入内容"
              v-model="formLabelEdit.message"
              disabled
            ></el-input>
          </el-form-item>
        </el-form>

        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogEditVisible = false">取 消</el-button>
          <el-button type="primary" @click="saveEdit(2)" v-if="dispose">已处理</el-button>
          <el-button type="primary" @click="saveEdit(3)" v-if="dispose">忽 略</el-button>
        </div>
      </el-dialog>
      <el-dialog
        title="订单详情"
        :visible.sync="dialogTableVisible"
      >
        <el-table :data="gridData">
          <el-table-column
          prop="orderNo"
          label="订单号"
          align="center"
          show-overflow-tooltip
        ></el-table-column>
        <el-table-column
          prop="userId"
          label="用户id"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="createBy"
          label="用户名"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="rentStartTime"
          label="租赁时间"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="rentEndTime"
          label="归还时间"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="rentTime"
          label="租赁时长"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="deposit"
          label="押金金额"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="rentAmount"
          label="订单金额"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="stateStr"
          label="订单状态"
          align="center"
        >
        </el-table-column>
        </el-table>
        <el-pagination
          @size-change="handleSizeChange2"
          @current-change="handleCurrentChange2"
          :page-sizes="[10, 20]"
          :current-page="1"
          :page-size="10"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total1"
        ></el-pagination>
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
      page1: 1,
      rows1: 10,
      idx: -1,
      status: -1,
      state: -1,
      title: "",
      flag: true,
      loading: true,
      options1: [],
      shopAddress: [],
      dataPick: "",
      dialogEditVisible: false,
      dialogTableVisible: false,
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
        userId: [{ required: true, message: "请选择用户", trigger: "change" }],
        message: [{ required: true, message: "请输入反馈内容", trigger: "blur" }],
        name: [{ required: true, message: "请输入姓名", trigger: "blur" }]
      },
       save:true,
      createExcel:true,
      dispose:true,
    };
  },
  mounted() {
    this.getUserList(this.formLabelAlign.page, this.formLabelAlign.rows);
    this.getChargeList();
    this.getBtnPms(this.$route.name)
    //新增权限
    this.btnList.some(item=>{
      return this.save = item.power == 'feedback_save'
    })
    //导出权限
    this.btnList.some(item=>{
      return this.createExcel = item.power == 'feedback_excel'
    })
    //处理权限
    this.btnList.some(item=>{
      return this.dispose = item.power == 'feedback_dispose'
    })
  },
  methods: {
    //  全选
    handleSelectionChange(val) {
      this.multipleSelection = val
      },
    //获取列表数据
    getUserList(page, rows) {
      console.log(rows);
      this.$axios
        .post(
          "/management/feedback/selectList",
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
    // 获取用户列表
    getChargeList(val) {
      this.$axios
        .post(
          `/management/users/selectList`,
          this.$qs.stringify({ page: 1, rows: 50, name: val })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.options1 = res.data.data.rows;
          }
        });
    },
    // dialog 打开前的回调
    open() {
      this.shopAddress = [];
      this.formLabelAdd ={};
    },
    //新增
    handleAdd(formName,status='') {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$axios
            .post(
              `/management/feedback/save?status=${status}`,
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
      if(rows.status==200){
        this.$message.error('反馈已处理！')
        return false
      }
      this.formLabelEdit = {};
      console.log(rows);
      this.idx = rows.id;
      this.status = rows.status;
      this.formLabelEdit = {
        message: rows.message
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
              `/management/feedback/dispose?id=${this.idx}&status=${state}`,
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
    handleCheck(index,row){
      console.log(row);
      this.dialogTableVisible = true;
      this.idx = row.user_id
      this.$axios
        .post(
          `/management/orders/selectOrdersList?userId=${row.user_id}`,
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
      // window.location.href = `http://192.168.51.60:8401/management/feedback/createExcel?id=${ids}`;
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
        .post(`/management/feedback/createExcel`,this.$qs.stringify({ids:ids}),{
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
            elink.download = "用户反馈报表.xls";
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
    },
    handleSizeChange2(val) {
      this.rows1 = val;
      this.$axios
        .post(
          `/management/orders/selectOrdersList?userId=${this.idx}`,
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
    handleCurrentChange2(val) {
      this.page1 = val;
      this.$axios
        .post(
          `/management/orders/selectOrdersList?userId=${this.idx}`,
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