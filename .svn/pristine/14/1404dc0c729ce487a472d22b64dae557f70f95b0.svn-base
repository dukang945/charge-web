
<template>
  <div class="tags" v-if="showTags">
    <div class="tab-bar">
      <span class="title-bar" style="margin-bottom:5px;color:#30133">{{$route.meta[0]}}</span>
      <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/workbench' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item v-for="(item,index) in $route.meta" :key="index">{{item}}</el-breadcrumb-item>
      </el-breadcrumb>
    </div>
    <ul>
      <li
        class="tags-li"
        v-for="(item,index) in tagsList"
        :class="{'active': isActive(item.path)}"
        :key="index"
      >
        <router-link :to="item.path" class="tags-li-title">{{item.title}}</router-link>
        <span class="tags-li-icon" @click="closeTags(index)">
          <i class="el-icon-close"></i>
        </span>
      </li>
    </ul>
    <!-- <div class="tags-close-box">
      <el-dropdown @command="handleTags">
        <el-button size="mini" type="primary">标签选项
          <i class="el-icon-arrow-down el-icon--right"></i>
        </el-button>
        <el-dropdown-menu size="small" slot="dropdown">
          <el-dropdown-item command="other">关闭其他</el-dropdown-item>
          <el-dropdown-item command="all">关闭所有</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </div>-->
  </div>
</template>

<script>
import bus from "./bus";
export default {
  data() {
    return {
      tagsList: [],
      data: 123,
      levelList: null,
      title: "首页"
    };
  },
  methods: {
    isActive(path) {
      return path === this.$route.fullPath;
    },
    // 关闭单个标签
    closeTags(index) {
      const delItem = this.tagsList.splice(index, 1)[0];
      const item = this.tagsList[index]
        ? this.tagsList[index]
        : this.tagsList[index - 1];
      if (item) {
        delItem.path === this.$route.fullPath && this.$router.push(item.path);
      } else {
        this.$router.push("/");
      }
    },
    // 关闭全部标签
    closeAll() {
      this.tagsList = [];
      this.$router.push("/");
    },
    // 关闭其他标签
    closeOther() {
      const curItem = this.tagsList.filter(item => {
        return item.path === this.$route.fullPath;
      });
      this.tagsList = curItem;
    },
    // 设置标签
    setTags(route) {
      const isExist = this.tagsList.some(item => {
        return item.path === route.fullPath;
      });
      if (!isExist) {
        if (this.tagsList.length >= 8) {
          this.tagsList.shift();
        }
        this.tagsList.push({
          title: route.name,
          path: route.fullPath
          // name: route.matched[1].components.default.name
        });
      }
      bus.$emit("tags", this.tagsList);
    },
    handleTags(command) {
      command === "other" ? this.closeOther() : this.closeAll();
    },
    // getBreadcrumb() {
    //   // 前三个，只拿数组[0]的值；
    //   let matched = this.$route.matched.filter(item => item.name);
    //   // console.log(matched[matched.length-2].name)
    //   this.title = matched[matched.length - 2].name;
    //   const first = matched[0];
    //   if (first && first.path == "/") {
    //     matched = {};
    //   } else if (
    //     first &&
    //     (first.path == "/userList" || first.path == "/infoList")
    //   ) {
    //     matched = [matched[0]];
    //   }
    //   this.levelList = matched;
    // }
  },
  computed: {
    showTags() {
      return this.tagsList.length > 0;
    }
  },
  watch: {
    $route(newValue, oldValue) {
      this.setTags(newValue);
    }
  },
  created() {
    this.setTags(this.$route);
  }
};
</script>


<style>
.tags {
  position: relative;
  height: 60px;
  /* overflow: hidden; */
  /* background: #fff;
  padding-right: 120px;
  box-shadow: 0 5px 10px #ddd; */
}
.tab-bar {
  float: left;
}
.tab-bar .title-bar {
  font-size: 16px;
  color: #303133;
  display: block;
  margin-bottom: 7px !important;
}
.tags ul {
  box-sizing: border-box;
  height: 100%;
  float: left;
  margin-left: 20px;
}
 
.tags-li {
  float: left;
  margin: 10px 5px 2px 3px;
  height: 30px;
  border-radius: 4px;
  overflow: hidden;
  cursor: pointer;
  line-height: 30px;
  border: 1px solid #7ca4ff;
  background: #edf3ff;
  padding: 0 5px 0 12px;
  vertical-align: middle;
  font-size: 14px;
  color: #7ca4ff;
  -webkit-transition: all 0.3s ease-in;
  -moz-transition: all 0.3s ease-in;
  transition: all 0.3s ease-in;
}


.tags-li.active {
  color: #fff;
 border: 1px solid #7ca4ff;
  background-color: #7ca4ff; 
} 
.tags-li.active .tags-li-title{
  color: #fff;
}
  .tags-li-title{
  float: left;
  max-width: 80px;
  overflow: hidden;
  white-space: nowrap;
  text-decoration: none;
  text-overflow: ellipsis;
  margin-right: 5px;
  color: #7ca4ff;
} 

.tags-close-box {
  position: absolute;
  right: 0;
  top: 0;
  box-sizing: border-box;
  padding-top: 1px;
  text-align: center;
  width: 110px;
  height: 30px;
  background: #fff;
  box-shadow: -3px 0 15px 3px rgba(0, 0, 0, 0.1);
  z-index: 10;
}
.el-breadcrumb__item:last-child .el-breadcrumb__inner{
  color: #7CA4FF !important;
} 
.el-breadcrumb__inner{
  color: #303133 !important;
  font-weight: normal !important;
}
.el-breadcrumb__inner a:hover, .el-breadcrumb__inner.is-link:hover{
  color: #7ca4ff !important;
}
</style>
