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
        <el-form-item label="店铺">
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
        <!-- <el-button type="primary" @click="dialogAddVisible = true">新增</el-button> -->
        <el-button
          type="primary"
          @click="handleExport"
          v-if="excel"
        >导出</el-button>
      </div>
      <el-table
        :data="userList"
        border
        style="width: 100%"
        @filter-change="filterTag"
      >
        <el-table-column
          prop="shopId"
          label="店铺ID"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="shopName"
          label="店铺名称"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="accno"
          label="银行卡号"
          align="center"
          show-overflow-tooltip
        ></el-table-column>
        <el-table-column
          prop="allMoney"
          label="分成收益"
          align="center"
        >
          <!-- <template slot-scope="scope">
            <span v-if="scope.row.shopdivide">{{scope.row.shopdivide}}:{{scope.row.sxypdivide}}</span>
          </template> -->
        </el-table-column>
        <el-table-column
          prop="money"
          label="提现金额"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="createAt"
          label="申请时间"
          align="center"
          width="200"
        ></el-table-column>
        <el-table-column
          prop="userName"
          label="操作人"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="cashsatus"
          label="状态"
          align="center"
        >
          <template slot-scope="scope">
            <span
              v-if="scope.row.cashsatus==2"
              style="color:#67C23A"
            >审核通过</span>
            <span
              v-if="scope.row.cashsatus==1"
              style='color:#909399'
            >待审核</span>
            <span
              v-if="scope.row.cashsatus==5"
              style='color:#F56C6C'
            >已拒绝</span>
            <span
              v-if="scope.row.cashsatus==4"
              style='color:#409EFF'
            >已打款</span>
          </template>
        </el-table-column>
        <el-table-column
          label="操作"
          align="center"
          width="350"
        >
          <template slot-scope="scope">
            <el-button
              size="mini"
              @click="handleEdit(scope.row,'SUCCESS')"
              :disabled="scope.row.cashsatus !==1"
              v-if="edit"
            >审核通过</el-button>
            <el-button
              size="mini"
              @click="handleEdit(scope.row,'REFUSED')"
              :disabled="scope.row.cashsatus !==1"
              v-if="edit"
            >审核拒绝</el-button>
            <el-button
              size="mini"
              @click="handleEdit(scope.row,'MAKE_PAYMENT')"
              :disabled="scope.row.cashsatus!=2"
              v-if="pay"
            >已打款</el-button>
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
export default {
  data() {
    return {
      total: 1,
      total1: 1,
      page: 1,
      rows: 50,
      idx: -1,
      state: -1,
      title: "",
      flag: false,
      loading: true,
      userList: [],
      detailList: [],
      nameList: [],
      roleList: [],
      selectedOptions: [],
      formLabelAlign: {
        page: 1,
        rows: 10
      },
      formLabelSearch: {
        startPage: 1,
        pageSize: 10
      },
      formLabelAdd: { charges: [] },
      formLabelEdit: { charges: [] },
      props: {
        value: "label"
      },
      rules: {
        admin: [{ required: true, message: "请输入账号", trigger: "blur" }],
        password: [{ required: true, message: "请输入密码", trigger: "blur" }],
        shopDetailedAddress: [
          { required: true, message: "请输入地址", trigger: "blur" }
        ]
      },
      excel:true,
      edit:true,
      pay:true,
    };
  },
  mounted() {
    this.getUserList(this.formLabelSearch.startPage, this.formLabelSearch.pageSize);
    this.getChargesList();
    this.getShopList();
    this.getBtnPms(this.$route.name)
    //新增权限
    this.btnList.some(item=>{
      return this.excel = item.power == 'withdraw_excel'
    })
    //编辑权限
    this.btnList.some(item=>{
      return this.edit = item.power == 'withdraw_edit'
    })
    //删除权限
    this.btnList.some(item=>{
      return this.pay = item.power == 'withdraw_pay'
    })
  },
  methods: {
    //获取列表数据
    getUserList(page, rows) {
      console.log(rows);
      this.$axios
        .post(
          "/management/operator/cash/getCashDrawsRecordsBackstage",
          JSON.stringify(this.formLabelSearch),
          {
            headers: {
              "Content-Type": "application/json;"
            }
          }
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            if (res.data.code == 200) {
              this.userList = res.data.data.cashDrawsBackVos;
              this.total = res.data.data.dataAcount;
            }else if(res.data.code == 509){
              this.userList = [];
              this.dataAcount = 0;
            } else {
              this.$message({
                type: "warning",
                message: `加载失败!`
              });
            }
          }
        });
    },
    // 状态筛选、
    filterTag(value) {
      this.formLabelSearch.cashsatus = Object.values(value)[0][0];
      this.getUserList(1, 10);
    },
    //获取充电柜列表
    getChargesList(val) {
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
            this.options1 = res.data.data.rows;
          }
        });
    },
    //获取店铺列表
    getShopList(val) {
      this.$axios
        .post(
          "/management/salesman/selectList",
          this.$qs.stringify({ page: 1, rows: 50, username: val })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            if (res.data.code == 200) {
              this.options3 = res.data.data.rows;
            } else {
              this.$message({
                type: "warning",
                message: `加载失败!`
              });
            }
          }
        });
    },
    //新增
    //dialog打开的回调
    open() {
      if (this.$refs["formLabelAdd"]) {
        this.$refs["formLabelAdd"].resetFields();
      }
      if (this.$refs["formLabelEdit"]) {
        this.$refs["formLabelEdit"].resetFields();
      }
      this.formLabelAdd = { charges: [] };
      this.shopAddress = this.city = [];
      this.businessStart = "09:00";
      this.businessEnd = "22:00";
      this.formLabelAlign = { charges: [] };
      this.fileList = [];
      this.fileList2 = [];
    },
    // handleAdd(formName) {
    //   console.log(this.businessStart);
    //   this.$refs[formName].validate(valid => {
    //     if (valid) {
    //       this.formLabelAdd.charges = this.formLabelAdd.charges.toString();
    //       this.formLabelAdd.businessStart =
    //         "1970-01-01" + " " + this.businessStart + ":00";
    //       this.formLabelAdd.businessEnd =
    //         "1970-01-01" + " " + this.businessEnd + ":00";
    //       console.log(this.formLabelAdd);
    //       this.$axios
    //         .post(
    //           "/management/shop/saveShop",
    //           this.$qs.stringify(this.formLabelAdd)
    //         )
    //         .then(res => {
    //           if (res.status == 200) {
    //             if (res.data.code == 506) {
    //               this.$message({
    //                 type: "warning",
    //                 message: `账号重复`
    //               });
    //               return false;
    //             } else if (res.data.code == 507) {
    //               this.$message({
    //                 type: "warning",
    //                 message: `手机号重复`
    //               });
    //               return false;
    //             }
    //             this.$message({
    //               type: "success",
    //               message: `添加成功!`
    //             });
    //             this.dialogAddVisible = false;
    //             this.getUserList(1, 10);
    //             this.$refs[formName].resetFields();
    //             this.formLabelAdd = {};
    //           }
    //         });
    //     } else {
    //       console.log("error submit!!");
    //       return false;
    //     }
    //   });
    // },
    // 审核
    handleEdit(row, state) {
      this.$confirm("确认执行该操作？", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        this.$axios
          .post(
            `/management/operator/cash/auditCashDrawsRequest?outTradeNo=${row.outTradeNo}&cashDrawStatusEnum=${state}&auditRemarks=1`
          )
          .then(res => {
            if (res.status == 200) {
              this.$message.success("执行成功");
              this.getUserList(
                this.formLabelSearch.page,
                this.formLabelSearch.rows
              );
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
          .get(`/management/shop/deleteShop?ids=${rows.shopid}`)
          .then(res => {
            if (res.status == 200) {
              this.$message.success("删除成功");
              this.getUserList(
                this.formLabelSearch.page,
                this.formLabelSearch.rows
              );
            }
          });
      });
    },
    //导出数据
    handleExport() {
      window.location.href = `http://192.168.51.60:8401/management/operator/cash/downloadCashDrawsRecordsExcel?shopName=${
        this.formLabelSearch.shopName?this.formLabelSearch.shopName:''
      }`;
      // this.$axios.post('/management/operator/cash/downloadCashDrawsRecordsExcel').then(res=>{
      //   if(res.status==200){
      //     console.log(res)
      //   }
      // })
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
    //选择地址
    addressChange(arr) {
      console.log(arr);

      this.formLabelAdd.shopAddress = arr.join(" ");
      if (this.dialogEditVisible) {
        this.formLabelEdit.shopAddress = arr.join(" ");
      }
    },
    cityChange(arr) {
      console.log(arr);
      this.formLabelAdd.city = arr.join(" ");
      if (this.dialogEditVisible) {
        this.formLabelEdit.city = arr.join(" ");
      }
    },
    //图片上传
    handleRemove(file, fileList) {
      this.formLabelAdd.imageUrl = "";
      if (this.dialogEditVisible) {
        this.formLabelEdit.imageUrl = "";
      }
    },
    handleRemove1(file, fileList) {
      this.formLabelAdd.contractImg = "";
      if (this.dialogEditVisible) {
        this.formLabelEdit.contractImg = "";
      }
    },
    handlePreview(file) {
      this.dialogVisible = true;
      this.dialogImageUrl = file.url;
    },
    beforeUpload(file) {
      this.imgData.FileName = file.name;
      this.imgData.imgFile = file;
      console.log(file);
      const isJPG =
        file.type == "image/png" ||
        file.type == "image/jpeg" ||
        file.type == "image/jpg";
      if (!isJPG) {
        this.$message.error("上传图片只能是 JPEG/PNG/JPG 格式!");
      }
      return isJPG;
    },
    upload(item) {
      let client = new OSS({
        region: "oss-cn-shanghai", // 服务器集群地区
        accessKeyId: "LTAIBlmIfZYuFy39", // OSS帐号
        accessKeySecret: "WtLJa4obg3fdExi3DL1v3XhT7LIufv", // OSS 密码
        bucket: "whsxdz-charge" // 阿里云上存储的 Bucket
      });
      let key =
        new Date().valueOf() +
        "_" +
        Math.round(Math.random() * 999999) +
        ".jpg"; // 存储路径，并且给图片改成唯一名字
      return client.put(key, item.file).then(res => {
        console.log(res);
        this.formLabelAdd.imageUrl = res.url;
        if (this.dialogEditVisible) {
          this.formLabelEdit.imageUrl = res.url;
        }
      }); // OSS上传
    },
    upload1(item) {
      let client = new OSS({
        region: "oss-cn-shanghai", // 服务器集群地区
        accessKeyId: "LTAIBlmIfZYuFy39", // OSS帐号
        accessKeySecret: "WtLJa4obg3fdExi3DL1v3XhT7LIufv", // OSS 密码
        bucket: "whsxdz-charge" // 阿里云上存储的 Bucket
      });
      let key =
        new Date().valueOf() +
        "_" +
        Math.round(Math.random() * 999999) +
        ".jpg"; // 存储路径，并且给图片改成唯一名字
      return client.put(key, item.file).then(res => {
        console.log(res);
        this.formLabelAdd.contractImg = res.url;
        if (this.dialogEditVisible) {
          this.formLabelEdit.contractImg = res.url;
        }
      }); // OSS上传
    },
    //分页
    handleSizeChange(val) {
      this.formLabelSearch.rows = val;
      this.getUserList(this.formLabelSearch.startPage, this.formLabelSearch.rows);
    },
    handleCurrentChange(val) {
      this.formLabelSearch.startPage = val;
      this.getUserList(this.formLabelSearch.startPage, this.formLabelSearch.rows);
    }
  }
};
</script>

<style>
.black {
  color: #fff;
  opacity: 0;
}
.el-form-item__content {
  line-height: 38px !important;
}
</style>