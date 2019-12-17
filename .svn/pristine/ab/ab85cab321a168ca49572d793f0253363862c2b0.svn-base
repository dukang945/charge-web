import Vue from 'vue'
import Router from 'vue-router'
import Main from '@/components/common/Main'
import Zhgl from '@/components/Jurisdiction/Zhgl'
import Login from '@/components/common/Login'
import sjxx from '@/components/sjxx'
import ddgl from '@/components/ddgl'
import sbxx from '@/components/sbxx'
import syxx from '@/components/syxx'
import sjfk from '@/components/sjfk'
import yhxx from '@/components/yhxx'
import yjgl from '@/components/yjgl'
import ywyxx from '@/components/ywyxx'
import wxryxx from '@/components/wxryxx'
import wxddgl from '@/components/wxddgl'
import bhgl from '@/components/bhgl'
import ywysy from '@/components/ywysy'
import yhfk from '@/components/yhfk'
import txgl from '@/components/txgl'
import systeam_permisson from '@/components/systeam_permisson'
import role_jurisdiction from '@/components/role_jurisdiction'
import workbench from '@/components/workbench'



Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      component: Main,
      name:'首页',
      redirect:'/workbench',
      children:[
        
        {
        path: '/sjxx',
        name: '商家信息',
        meta:['店铺管理','商家信息'],
        component: sjxx
      },
        {
        path: '/sjfk',
        name: '商家反馈',
        meta:['店铺管理','商家反馈'],
        component: sjfk
      },
        {
        path: '/ddgl',
        name: '订单管理',
        meta:['订单管理'],
        component: ddgl
      },
        {
        path: '/sbxx',
        name: '设备信息',
        meta:['设备管理','设备信息'],
        component: sbxx
      },
        {
        path: '/bhgl',
        name: '补货管理',
        meta:['设备管理','补货管理'],
        component: bhgl
      },
        {
        path: '/syxx',
        name: '收益信息',
        meta:['收益管理','收益信息'],
        component: syxx
      },
        {
        path: '/txgl',
        name: '提现管理',
        meta:['收益管理','提现管理'],
        component: txgl
      },
        {
        path: '/yhxx',
        name: '用户信息',
        meta:['用户管理','用户信息'],
        component: yhxx
      },
        {
        path: '/yhfk',
        name: '用户反馈',
        meta:['用户管理','用户反馈'],
        component: yhfk
      },
        {
        path: '/yjgl',
        name: '押金管理',
        meta:['收益管理','押金管理'],
        component: yjgl
      },
        {
        path: '/ywyxx',
        name: '业务员信息',
        meta:['业务员管理','业务员信息'],
        component: ywyxx
      },
        {
        path: '/ywysygl',
        name: '业务员收益',
        meta:['业务员管理','业务员收益'],
        component: ywysy
      },
        {
        path: '/wxryxx',
        name: '维修人员信息',
        meta:['维修人员管理','维修人员信息'],
        component: wxryxx
      },
        {
        path: '/wxddgl',
        name: '维修订单管理',
        meta:['维修人员管理','维修订单管理'],
        component: wxddgl
      },
        {
        path: '/systeam_permisson',
        name: '菜单管理',
        meta:['账号管理 ','菜单管理'],
        component: systeam_permisson
      },
        {
        path: '/role_jurisdiction',
        name: '角色管理',
        meta:['账号管理','角色管理'],
        component: role_jurisdiction
      },
      {
        path: '/zhgl',
        name: '账号管理',
        meta:['账号管理','账号管理'],
        component: Zhgl
      },
      
    {
      path: '/workbench',
      component: workbench,
      meta:['工作台'],
      name:'工作台',
    },
    ]
    },
    {
      path: '/login',
      component: Login,
      name:'登录',
    },
  ]
})
