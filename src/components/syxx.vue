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
        <el-form-item label="店铺名称">
          <el-input
            placeholder="请输入店铺名称"
            v-model="formLabelSearch.shopName"
            @keyup.enter.native="handleSearch"
          ></el-input>
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
        <h2 v-if="sumDeposit">收益总额：{{total$}}</h2><br>
        <el-button
          type="primary"
          @click="handleExport"
          v-if="excel"
        >批量导出</el-button>
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
        <el-table-column
          prop="id"
          label="店铺ID"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="shop_name"
          label="店铺名称"
          align="center"
          width="300"
        ></el-table-column>
        <el-table-column
          prop="allmoney"
          label="分成收益"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="rent_all_money"
          label="店铺总收益"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="all_earnings"
          label="已提现"
          align="center"
        >
          <template slot-scope="scope">
            <span>{{scope.row.allmoney-scope.row.now_money}}</span>
          </template>
        </el-table-column>
        <el-table-column
          prop="ordernum"
          label="订单数量"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="alltime"
          label="用户使用总时长"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="charge_num"
          label="持有台数"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="city"
          label="所在城市"
          align="center"
        ></el-table-column>
        <el-table-column
          label="操作"
          align="center"
          width="150"
        >
          <template slot-scope="scope">
            <el-button
              size="mini"
              @click="handleDetail(scope.row)"
            >查看提现记录</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-dialog
        title="提现记录"
        :visible.sync="dialogEditVisible"
        append-to-body
      >
        <el-table :data="gridData">
          <el-table-column
            property="outTradeNo"
            label="订单编号"
          ></el-table-column>
          <el-table-column
            property="createTime"
            label="提现时间"
          ></el-table-column>
          <el-table-column
            property="amount"
            label="提现金额"
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
      total$: -1,
      page: 1,
      rows: 1,
      idx: -1,
      state: -1,
      title: "",
      flag: true,
      loading: true,
      dialogEditVisible: false,
      userList: [],
      detailList: [],
      gridData: [],
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
        loginName: [{ required: true, message: "请输入账号", trigger: "blur" }],
        password: [{ required: true, message: "请输入密码", trigger: "blur" }],
        name: [{ required: true, message: "请输入姓名", trigger: "blur" }]
      },
      sumDeposit: true,
      excel: true
    };
  },
  mounted() {
    this.getUserList(this.formLabelSearch.page, this.formLabelSearch.rows);
    // 获取收益总额
    this.$axios
      .get("/management/shopWallet/getAllEarningsToAllShop")
      .then(res => {
        if (res.status == 200) {
          console.log(res);
          this.total$ = res.data.data;
        }
      });
    this.getBtnPms(this.$route.name);
    //获取押金总额权限
    this.btnList.some(item => {
      return (this.sumDeposit = item.power == "shop_getAllEarningsToAllShop");
    });
    //导出权限
    this.btnList.some(item => {
      return (this.excel = item.power == "shop_createShopMoneyExcel");
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
          "/management/shop/getShopMoneyList",
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
    //查看提现记录
    handleDetail(row) {
      this.dialogEditVisible = true;
      if (row) {
        this.idx = row.id;
      }
      this.$axios
        .post(
          `/management/wallet/getTransferByUserId`,
          this.$qs.stringify({
            userId: this.idx,
            page: this.page,
            rows: this.rows
          })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.gridData = res.data.data;
            this.total1 = res.data.data.length;
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
      // window.location.href = `http://192.168.51.60:8401/management/shop/createShopMoneyExcel?shopName=${
      //   this.formLabelSearch.shopName ? this.formLabelSearch.shopName : ""
      // }`;
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
        .post(`/management/shop/createShopMoneyExcel`,this.$qs.stringify({ids:ids}),{
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
            elink.download = "收益报表.xls";
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
      this.handleDetail();
    },
    handleCurrentChange1(val) {
      this.page = val;
      this.getUserList();
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