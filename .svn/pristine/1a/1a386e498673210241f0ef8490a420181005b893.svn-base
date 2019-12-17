<template>
  <div id="container">
    <el-container>
      <el-header>
        <img src="../../assets/images/logo.png" alt="" class="water-logo">
        <span class="main-title">水象优品充电宝租赁后台管理系统</span>
        <div class="container-left" @click="isCollapse=!isCollapse">
          <i class="icon-webicon03 iconfont collapse"></i>
          <!-- <icon name="icon-01" :scale="110"></icon> -->
        </div>

        <div class="container-right">
          <!-- <div class="portrait">
            <img src="../../assets/images/head.jpg" alt>
            <img :src="head" alt>
          </div>  -->
          <div class="info">
            <span>{{userName}}</span>
            <el-dropdown @command="handleCommand" trigger="click">
              <i class="el-icon-arrow-down el-icon--right"></i>
              <el-dropdown-menu size="small" slot="dropdown">
                <el-dropdown-item command="logout">退出</el-dropdown-item>
                <!-- <el-dropdown-item>查看</el-dropdown-item> -->
              </el-dropdown-menu>
            </el-dropdown>
          </div>
          <!-- el-tooltip   文字提示 -->
        </div>
        <div class="btn-fullscreen" @click="handleFullScreen">
          <el-tooltip effect="dark" :content="fullscreen ? `取消全屏`:`全屏`" placement="bottom">
            <i class="el-icon-rank"></i>
          </el-tooltip>
        </div>
      </el-header>
      <el-container>
        <aside class="el-aside">
          <el-menu
            router
            class="el-menu-vertical-demo"
            :collapse="isCollapse"
            :default-active="routePath"
          >
            <div class="logo" v-if="!isCollapse">
              <!-- <img :src="logo1" alt>
              <img :src="logo2" alt> -->
              <!-- <img src="../../assets/images/ball-1.png" alt>
              <img src="../../assets/images/logo.png" alt> -->
            </div>
            <h3 class="title" v-if="!isCollapse">平台管理</h3>
            <el-menu-item
              :index="item.power"
              v-for="item in menuList.filter(val=> !val.children[0].type==0 || val.type==1)"
              :key="item.id"
              class="main-menu"
            >
            <i :class="[item.descritpion,'iconfont']"></i>
              <span slot="title">{{ item.name }}</span>
            </el-menu-item>
            <!-- <el-menu-item class="workbench" index="workbench">首页</el-menu-item> -->
            <el-submenu
              :index="item.id.toString()"
              v-for="item in menuList.filter(val=>val.children && val.children[0].type==0)"
              :key="item.id"
            >
              <template slot="title">
                <!-- <i :class="[item.icon,'iconfont']" v-if="item.icon!=='el-icon-tickets'"></i> -->
                <i :class="[item.descritpion,'iconfont']"></i>
                <!-- <icon :name="item.descritpion" :scale="110"></icon> -->
                <span>{{item.name}}</span>
              </template>
              
              <el-menu-item-group class="menu-list">
                <el-menu-item
                  :index="item.power"
                  v-for="item in item.children.filter(val=>val.type==0)"
                  :key="item.id"
                >{{ item.name }}</el-menu-item>
              </el-menu-item-group>
            </el-submenu>
            
          </el-menu>
        </aside>

        <el-main>
          <div class="content-box" :class="{'content-collapse':collapse}">
            <v-tags></v-tags>
            <div class="content">
              <transition name="move" mode="out-in">
                <keep-alive :include="tagsList"></keep-alive>
              </transition>
            </div>
          </div>
          <transition name="move" mode="out-in">
            <router-view></router-view>
          </transition>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
import vTags from "./Tags.vue";
import bus from "./bus";
export default {
  data() {
    return {
      menuList: [
      ],
      tagsList: [],
      fullscreen: false,
      logo1: "../../assets/images/未标题-1.png",
      logo2: "../../assets/images/logo.png",
      head: "../../assets/images/head.jpg",
      list: {
        outTradeNo: "",
        payWay: "",
        startTime: "2019-01-15 11:07:25",
        page: "1",
        payState: "",
        endTime: "2019-01-16 14:47:46",
        rows: "10"
      },
      collapse: false,
      isCollapse: false
    };
  },
  components: {
    vTags
  },
  computed: {
    routePath() {
      let routePath = this.$route.path.substring(1);
      return routePath;
    },
    userName() {
      let userName = sessionStorage.getItem("userName");
      return userName;
    }
  },
  mounted() {
    // this.getMenuList();
    this.menuList = JSON.parse(sessionStorage.getItem('permissionList'))
    console.log(this.menuList)
  },
  methods: {
    getMenuList() {
      this.$axios
        .post(
          "/management/permisson/getTreeData"
          // this.$qs.stringify({ token: sessionStorage.getItem("token") })
        )
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.menuList = res.data.data;
            
          }
        });
    },
    handleCommand(command) {
      command === "logout" ? this.logout() : this.show();
    },
    //退出登录
    logout() {
      var id = sessionStorage.getItem('id')
      this.$axios
        .post(
          `/management/login/loginOut?id=${id}`
        )
        .then(res => {
          if (res.status == 200) {
            if (res.data.msg == "SUCCESS") {
              sessionStorage.clear();
              this.$router.push("/login");
            }
          }
        });
    },
    //全屏显示
    handleFullScreen() {
      let element = document.documentElement;
      if (this.fullscreen) {
        if (document.exitFullscreen) {
          document.exitFullscreen();
        } else if (document.webkitCancelFullScreen) {
          document.webkitCancelFullScreen();
        } else if (document.mozCancelFullScreen) {
          document.mozCancelFullScreen();
        } else if (document.msExitFullscreen) {
          document.msExitFullscreen();
        }
      } else {
        if (element.requestFullscreen) {
          element.requestFullscreen();
        } else if (element.webkitRequestFullScreen) {
          element.webkitRequestFullScreen();
        } else if (element.mozRequestFullScreen) {
          element.mozRequestFullScreen();
        } else if (element.msRequestFullscreen) {
          // IE11
          element.msRequestFullscreen();
        }
      }
      this.fullscreen = !this.fullscreen;
    }
  }
};
</script>

<style>
#container {
  height: 100%;
}
.el-icon-tickets:before {
  content: "\E63F";
  font-size: 16px;
}
/*修改滚动条样式*/
::-webkit-scrollbar{
  width:7px;
  height:7px;
  /**/
}
::-webkit-scrollbar-track{
  background: #e9eef3;
  border-radius:2px;
}
::-webkit-scrollbar-thumb{
  background: #bfbfbf;
  border-radius:10px;
}
::-webkit-scrollbar-thumb:hover{
  background: #ccc;
}
::-webkit-scrollbar-corner{
  background: #179a16;
}
.el-container {
  height: 100%;
}
.el-header {
  background: linear-gradient(90deg, #689dfd, #7c90fd);
  box-shadow: 2px 2px 3px 0 rgba(61, 61, 61, 0.5);
  line-height: 60px;
}
.el-menu {
  max-width: 200px;
}
.el-aside {
  color: #333;
  text-align: left;
  line-height: 200px;
  overflow-x: hidden !important;
  max-width: 200px;
  box-shadow: 2px 4px 2px #ccc;
  position: relative;
}
.logo {
  /* height: 200px; */
  max-width: 200px;
}
.title {
  font-size: 12px;
  color: #909399;
  margin-left: 16px;
  line-height: 16px;
  margin-top: 32px;
  margin-bottom: 5px;
}
.logo img {
  display: block;
}
.logo img:nth-of-type(1) {
  max-width: 55px;
  height: 55px;
  margin-left: 72.5px;
  margin-top: 50px;
}
.logo img:nth-of-type(2) {
  max-width: 166px;
  height: 20px;
  margin: 0;
  margin-left: 17px;
  margin-top: 23px;
}
.el-submenu__title:hover {
  background-color: #edf3ff;
  color: #7ca4ff;
  font-size: 14px;
}
.el-submenu__title:hover i {
  color: #7ca4ff;
}
.el-menu-item {
  position: relative;
  text-align: left !important;
  padding-left: 60px !important;
}
.main-menu{
  padding-left: 20px !important;
}
/* .el-menu-item::before {
  content: "";
  width: 4px;
  height: 4px;
  position: absolute;
  border-radius: 50%;
  left: 45px;
  top: 23px;
  border: 1px solid #999999;
  box-sizing: border-box;
}
.el-menu-item.is-active::before {
  border: 0;
  background-color: #7ca4ff;
} */
.el-menu-vertical-demo:not(.el-menu--collapse) {
  width: 200px;
  min-height: 400px;
}
.el-submenu .el-menu-item {
  text-align: center;
}
.el-submenu [class^="el-icon-"] {
  width: 15px;
  margin-right: 0;
}
.el-main {
  background-color: #e9eef3;
  color: #333;
  padding: 0 !important;
}
.content-box {
  padding: 20px;
}
.contain-box {
  padding: 0 20px;
  height: 87%;
}
.el-select{
  width:100%;
}
.el-cascader{
  width:100%;
}
.table-box {
  padding: 23px 32px;
  padding-bottom: 0;
  background-color: #fff;
  border-radius: 4px;
  box-shadow: 2px 4px 5px 0 rgba(238, 238, 238, 0.5);
  margin-top: 20px;
}
.table-box:nth-of-type(1) {
  margin-top: -20px;
}

.function {
  padding-bottom: 20px;
}
.el-table td,
.el-table th {
  padding: 8px 0 !important;
}
.el-pagination {
  text-align: right;
  padding-top: 20px !important;
  padding-bottom: 40px !important;
}
.container-right {
  height: 100%;
  float: right;
}
.container-right .portrait {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  float: left;
  margin-top: 10px;
  margin-right: 20px;
}
.portrait img {
  width: 100%;
  border-radius: 50%;
}
.container-right .info {
  float: right;
  color: #fff;
  font-size: 14px;
}
.el-dropdown {
  display: inline;
}
.container-right .info i {
  cursor: pointer;
  color: #fff;
}
.container-left {
  float: left;
  cursor: pointer;
  margin-left: 200px;
  color: #fff;
}
.handle-oprate {
  margin-left: 40px;
}
.el-button--primary {
  background-color: #7ca4ff !important;
  border-color: #7ca4ff !important;
}
.el-menu-item.is-active {
  color: #7ca4ff !important;
}
.el-button--small {
  font-size: 14px;
  border-radius: 4px;
  padding: 9px 12px;
}
/* .handle{
  margin-left: 2px !important;
} */
/* .el-submenu .el-menu-item{
  color: #999;
} */
.save-msg {
  float: right;
  margin-top: 90px !important;
  margin-bottom: 20px !important;
}
.el-dialog__header {
  text-align: center;
  background: #ebeef5;
  border-radius: 8px 8px 0px 0px;
}
.dialog-footer {
  text-align: center;
}
.el-dialog--center .el-dialog__footer {
  text-align: center !important;
}
.el-table .has-gutter th {
  background: #ebeef5;
}
.el-table--border th {
  border-right: 1px solid #dcdfe6 !important;
}
.container-left .collapse {
  font-size: 24px;
}
.el-table th > .cell {
  padding: 7px;
}
.cell .el-button--danger.is-plain {
  background-color: #fff;
  border: 1px solid #f56c6c;
}
.el-button--danger.is-plain:focus,
.el-button--danger.is-plain:hover {
  background-color: #fef0f0 !important;
  color: #f56c6c !important;
}
.svg-icon {
  color: #a0a0a0;
  margin-right: 15px;
}
.el-submenu__title:hover .svg-icon {
  color: #7ca4ff;
}
.online .el-dialog {
  top: 20%;
}
.online .el-dialog__body {
  padding: 0 !important;
}
.online .el-dialog__footer {
  margin: 54px auto;
  padding-bottom: 54px;
}
.el-input.is-active .el-input__inner,
.el-input__inner:focus {
  border-color: #7ca4ff !important;
}
.el-dialog {
  text-align: left !important;
}
.input::-webkit-input-placeholder {
  color: #e4e7ed;
}
.input:-moz-placeholder {
  color: #e4e7ed;
}
.input:-ms-input-placeholder {
  color: #e4e7ed;
}
.btn-fullscreen {
  float: right;
  color: #fff;
  margin-right: 10px;
}
.menu-list{
  /* border-bottom: 1px solid #ccc; */
  /* box-shadow: 13px 4px 8px 1px #e4e7ed; */
}
.main-title{
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  color: #fff;
  font-size: 20px;
}
.water-logo{
  position: absolute;
  width: 200px;
  margin-top: 18px;
}
.iconfont{
  color: #333 !important;
}
</style>