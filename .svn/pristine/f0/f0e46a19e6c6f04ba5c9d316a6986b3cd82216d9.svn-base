// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import App from './App'
import router from './router'
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
import axios from 'axios'
import qs from 'qs'
import './assets/icon/iconfont.css'
// import Icon from 'vue-svg-icon/Icon.vue';
// Vue.component('icon', Icon);
//兼容ie 的promise 处理
import promise from 'es6-promise'
promise.polyfill();
 //省市区
 import VueAreaLinkage from 'vue-area-linkage';
// echarts
import echarts from 'echarts'

Vue.prototype.$echarts = echarts
Vue.use(VueAreaLinkage)
Vue.prototype.$axios = axios;
// 定义按钮权限公共方法
Vue.prototype.getBtnPms = function (pathName) { 
  console.log(pathName)
  var PmsList = JSON.parse(sessionStorage.getItem('permissionList'))
  for (let index = 0; index < PmsList.length; index++) {
    for (let i = 0; i < PmsList[index].children.length; i++) {
      if(PmsList[index].children[i].type==0){
        if(PmsList[index].children[i].name == pathName){
          let btnList = PmsList[index].children[i].children
          console.log(btnList)
          Vue.prototype.btnList = btnList
        }
      }else{
        if(PmsList[index].name == pathName){ 
          let btnList = PmsList[index].children
          console.log(btnList)
          Vue.prototype.btnList = btnList

        }
      }
      
    }
    
  }
 };
 
Vue.prototype.$qs = qs;


Vue.use(ElementUI);

Vue.config.productionTip = false
// 路由拦截
router.beforeEach((to, from, next) => {
  const role = sessionStorage.getItem('id');
  if (!role && to.path !== '/login') {
    next('/login');
  } else if (to.meta.permission) {
    // 如果是管理员权限则可进入
    role === 'admin' ? next() : next('/403');
  } else {
    // 简单的判断IE10及以下不进入富文本编辑器，该组件不兼容
    if (navigator.userAgent.indexOf('MSIE') > -1 && to.path === '/editor') {
      Vue.prototype.$alert('vue-quill-editor组件不兼容IE10及以下浏览器，请使用更高版本的浏览器查看', '浏览器不兼容通知', {
        confirmButtonText: '确定'
      });
    } else {
      next();
    }
  }
})
//请求头添加token
axios.interceptors.request.use(
  config => {
    if (sessionStorage.getItem('token') && config.url.split('?')[0] !== 'https://restapi.amap.com/v3/geocode/geo') {
      config.headers.authStr = sessionStorage.getItem('token');
    }
 
    return config;
  },
  error => {
    return Promise.reject(error);
  });
  // 响应拦截
  axios.interceptors.response.use((res) => {
    if (res.data.code ==504) {
      console.log(res)
      // this.$message.warning('token过期，请重新登录！')
      router.push("/login")
      
    //其余错误状态处理    
    }else if(res.data.code ==526){
      this.$message.warning('没有该操作权限')
    }
    return res
  }, function (error) {
    return Promise.reject(error);
  });
/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})
