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
        <el-form-item label="商家id">
          <el-input
            placeholder="请输入商家id"
            v-model="formLabelSearch.shopId"
            @keyup.enter.native="handleSearch"
          ></el-input>
        </el-form-item>
        <el-form-item label="状态">
          <el-select
            v-model="formLabelSearch.state"
            placeholder="请选择状态"
          >
            <el-option
              label="全部"
              value=""
            ></el-option>
            <el-option
              label="进行中"
              value="0"
            ></el-option>
            <el-option
              label="已完成"
              value="1"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleSearch">查询</el-button>
          <!-- <el-button @click="handleReset">重置</el-button> -->
        </el-form-item>
      </el-form>
    </div>
    <div class="table-box">
      <div class="function">
        <el-button type="primary" @click="dialogAddVisible = true" v-if="save">新增</el-button>
        <el-dialog title="新增" :visible.sync="dialogAddVisible" @open='open'>
          <el-form
            :model="formLabelAdd"
            :rules="rules"
            ref="formLabelAdd"
            label-position="right"
            label-width="80px"
          >
          <el-form-item label="设备类型" prop="facilityType">
              <el-radio v-model="formLabelAdd.facilityType" label="0">充电宝</el-radio>
              <el-radio v-model="formLabelAdd.facilityType" label="1">充电柜</el-radio>
            </el-form-item>
            
            <el-form-item label="设备编号" prop="facilityNo">
               <el-select
                v-model="formLabelAdd.facilityNo"
                multiple
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
                  :value="item.chargeNumber"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="补货数量" >
              <el-input v-model="formLabelAdd.num" disabled></el-input>
            </el-form-item>
            <el-form-item label="商家id" prop="shopId">
              <el-input v-model="formLabelAdd.shopId"></el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogAddVisible = false">取 消</el-button>
            <el-button type="primary" @click="handleAdd('formLabelAdd')">确 定</el-button>
          </div>
        </el-dialog>
      </div>
      <el-table :data="userList" border style="width: 100%" @filter-change="filterTag">
        <el-table-column prop="facility_no" label="设备编号" align="center" show-overflow-tooltip></el-table-column>
        <el-table-column prop="shopid" label="商家id" align="center"></el-table-column>
        <el-table-column prop="facility_type" label="设备类型" align="center">
          <template slot-scope="scope">
            <span v-if="scope.row.facility_type==0">充电宝</span>
            <span v-if="scope.row.facility_type==1">充电柜</span>
          </template>
        </el-table-column>
        <el-table-column prop="num" label="补货数量" align="center"></el-table-column>
        <el-table-column prop="shop_detailed_address" label="所在区域" align="center" show-overflow-tooltip></el-table-column>
        <el-table-column prop="replenishment_state" label="订单状态" align="center" >
          <template slot-scope="scope">
            <span v-if="scope.row.replenishment_state==0" style='color:#909399'>进行中</span>
            <span v-if="scope.row.replenishment_state==1" style="color:#67C23A">已完成</span>
          </template>
        </el-table-column>
        <el-table-column prop="phone" label="商家手机号" align="center"></el-table-column>
        <el-table-column prop="name" label="业务员" align="center"></el-table-column>
        <el-table-column label="操作" align="center">
          <template slot-scope="scope">
            <el-button size="mini" @click="handleEdit(scope.$index, scope.row)" :disabled="scope.row.replenishment_state==1" v-if="accomplish">订单完成</el-button>
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
      onlineDialogVisible: false,
      userList: [],
      detailList: [],
      nameList: [],
      roleList: [],
      permissionList: [],
      options: [],
      options1: [],
      shopAddress: [],
      props:{
        value:'label'
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
      rules: {
        facilityNo: [
          { required: true, message: "请选择编号", trigger: "blur" }
        ],
        facilityType: [
          { required: true, message: "请选择类型", trigger: "change" }
        ],
        shopId: [
          { required: true, message: "请输入商家id", trigger: "blur" }
        ]
      },
      //权限flag
      save:true,
      accomplish:true,

    };
  },
  computed: {
    numAdd(){
      return this.formLabelAdd.facilityNo
    }
  },
  watch: {
    numAdd(val){
      this.formLabelAdd.num = val.length
    }
  },
  mounted() {
    this.getUserList(this.formLabelSearch.page, this.formLabelSearch.rows);
    this.getChargesList('')
    this.getBtnPms(this.$route.name)
    //新增权限
    this.btnList.some(item=>{
      return this.save = item.power == 'replenishmentOrder_save'
    })
    //完成订单权限
    this.btnList.some(item=>{
      return this.accomplish = item.power == 'replenishmentOrder_accomplish'
    })
  },
  methods: {
    //获取列表数据
    getUserList(page, rows) {
      console.log(rows);
      this.$axios
        .post(
          "/management/replenishmentOrder/selectList",
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
            type:this.formLabelAdd.facilityType,
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
      this.formLabelSearch.state = Object.values(value)[0][0];
      this.getUserList(1, 10);
    },
    //dialog打开回调 
    open(){
      this.formLabelAdd ={}
      this.shopAddress = []
    },
    //新增
    handleAdd(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
            this.formLabelAdd.facilityNo = this.formLabelAdd.facilityNo.join(',')
            // this.fo
          this.$axios
            .post(
              "/management/replenishmentOrder/save",
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
       this.$confirm("确认完成改订单吗？", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        this.$axios
          .get(`/management/replenishmentOrder/accomplish?id=${rows.id}`)
          .then(res => {
            if (res.status == 200) {
              this.$message.success("已完成");
              this.getUserList(1, 10);
            }
          });
      });
    },
    //选择地址
    addressChange(arr) {
      console.log(arr);
     
      this.formLabelAdd.address = arr.join(' ')
      if(this.dialogEditVisible){
        this.formLabelEdit.address = arr.join(' ')
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