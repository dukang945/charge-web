<template>
  <div class="contain-box">
    <div v-if="checkout" style="margin-top:-20px">
      <el-row>
        <el-card class="box-card">
          <el-col :span="10">
            <h3 class="sale-title">销售总额</h3>
          </el-col>
          <el-col :span="14">
            <div class="timepick">
              <el-button
                type="text"
                @click="getsaleRank(0)"
              >今日</el-button>
              <el-button
                type="text"
                @click="getsaleRank(1)"
              >本周</el-button>
              <el-button
                type="text"
                @click="getsaleRank(2)"
              >本月</el-button>
              <el-button
                type="text"
                @click="getsaleRank(3)"
              >今年</el-button>
              <el-date-picker
                v-model="value"
                type="daterange"
                size='small'
                @change="getsaleRank(4)"
                range-separator="至"
                start-placeholder="开始日期"
                end-placeholder="结束日期"
                value-format="yyyy-MM-dd HH:mm:ss"
              >
              </el-date-picker>
            </div>

          </el-col>
        </el-card>
      </el-row>
      <el-row>
        <el-col
          :span="16"
          class="left"
        >
          <el-card class="box-card">
            <div class="tip-title">
              销售额趋势
            </div>
            <div
              ref="salesEchart"
              class="salesEchart"
            ></div>
          </el-card>
        </el-col>
        <el-col :span="8">
          <el-card class="box-card">
            <div class="tip-title">
              门店销售额排名
            </div>
            <ul class="sale-rank">
              <li
                v-for=" item,index in saleData"
                :key="item.id"
              >
                <el-row>
                  <el-col :span="3">
                    <div
                      class="cicle-box cicle-box-1"
                      v-if="index==0"
                    ></div>
                    <div
                      class="cicle-box cicle-box-2"
                      v-else-if="index==1"
                    ></div>
                    <div
                      class="cicle-box cicle-box-3"
                      v-else-if="index==2"
                    ></div>
                    <div
                      class="cicle-box cicle-box-last"
                      v-else-if='index<8'
                    >{{index+1}}</div>
                  </el-col>
                  <el-col :span="15">
                    <div class="content">{{item.shop_name}}</div>
                  </el-col>
                  <el-col
                    :span="6"
                    style="text-align:right;color:#7CA4FF"
                  >
                    <div>{{item.money}}</div>
                  </el-col>
                </el-row>
              </li>
            </ul>
          </el-card>
        </el-col>
      </el-row>
      <el-row>
        <el-col
          :span='8'
          class="left"
        >
          <el-card class="box-card ">
            <div class="tip-title">
              维修订单统计
            </div>
            <div
              ref="orderEchart"
              class="orderEchart"
            ></div>
          </el-card>
        </el-col>
        <el-col
          :span='8'
          class="left"
        >
          <el-card class="box-card">
            <div class="tip-title">
              押金总额统计
            </div>
            <div
              ref="depositEchart"
              class="depositEchart"
            ></div>
            <div class="msg-footer">
              <div>
                <el-row style="text-align:right">
                  <el-col :span='10'>
                    <span class="point-1"></span>
                    <span>未提取</span>
                    <el-divider direction="vertical"></el-divider>
                  </el-col>
                  <el-col :span='3'>
                    <span style="color:#999">{{returnMoneyPer}}</span>
                  </el-col>
                  <el-col :span='4'>
                    <span >￥{{returnMoney}}</span>
                  </el-col>
                </el-row>
              </div>
              <div>
                <el-row style="text-align:right">
                  <el-col :span='10'>
                    <span class="point-2"></span>
                    <span>已换购</span>
                    <el-divider direction="vertical"></el-divider>
                  </el-col>
                  <el-col :span='3'>
                    <span style="color:#999">{{changeMoneyPer}}</span>
                  </el-col>
                  <el-col :span='4'>
                    <span >￥{{changeMoney}}</span>
                  </el-col>
                </el-row>
              </div>
            </div>
          </el-card>
        </el-col>
        <el-col :span='8'>
          <el-card class="box-card">
            <div class="tip-title">
              商家反馈问题统计
            </div>
            <ul class="feedback-rank">
              <li
                v-for=" item,index in feedbackData"
                :key="item.id"
              >
                <el-row>
                  <el-col :span="3">
                    <div
                      class="cicle cicle-1"
                      v-if="index==0"
                    ></div>
                    <div
                      class="cicle cicle-2"
                      v-else-if="index==1"
                    ></div>
                    <div
                      class="cicle cicle-3"
                      v-else-if="index==2"
                    ></div>
                    <div
                      class="cicle"
                      v-else-if='index<8'
                    ></div>
                  </el-col>
                  <el-col :span="15">
                    <div class="content">{{item.problem_describe}}</div>
                  </el-col>
                  <el-col
                    :span="6"
                    style="text-align:right;color:#7CA4FF"
                  >
                    <div>{{item.num}}</div>
                  </el-col>
                </el-row>
              </li>
            </ul>
          </el-card>
        </el-col>
      </el-row>
    </div>
    <div
      v-else
      class="notfound"
      
    >暂无权限！</div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      chart1: null,
      chart2: null,
      chart3: null,
      checkout: false,
      returnMoney: 0,
      changeMoney: 0,
      returnMoneyPer: 0,
      changeMoneyPer: 0,
      saleData: [],
      feedbackData: [],
      monthData: [],
      monthData2: [],
      monthData3: [],
      YData: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
      YData2: [0, 0, 0, 0, 0, 0],
      YData3: [],
      value: []
    };
  },
  mounted() {
    this.getDate(6);
    this.getDate(12);
    this.getsaleColumn();
    this.getOrderList();
    this.getDeposit();
    setTimeout(() => {
      this.initChart1();
      this.initChart2();
      this.initChart3();
    }, 500);
    this.getsaleRank(3);
    this.getfeedback();
    this.getBtnPms(this.$route.name);
    //查看权限
    this.btnList.some(item => {
      return (this.checkout = item.power == "workbench_checkout");
    });
  },
  methods: {
    // 获取当前年月，根据当前年月前推12个月，渲染x坐标数据
    getDate(month) {
      var d = new Date();
      for (var i = 0; i < month; i++) {
        d.setMonth(d.getMonth() - 1);
        var m = d.getMonth() + 2;

        if (month == 12) {
          m = m < 10 ? "0" + m : m;
          this.monthData.unshift(d.getFullYear() + "-" + m);
        } else {
          this.monthData2.unshift(m + "月");
          m = m < 10 ? "0" + m : m;
          this.monthData3.unshift(d.getFullYear() + "-" + m);
        }
      }
      console.log(this.monthData);
    },
    //获取销售额趋势数据
    getsaleColumn() {
      this.$axios
        .get("/management/dataStatistics/selectMoneyByMonth")
        .then(res => {
          if (res.status == 200) {
            console.log(res.data.data);
            for (let i = 0; i < this.monthData.length; i++) {
              for (let j = 0; j < res.data.data.length; j++) {
                if (res.data.data[j].timestr === this.monthData[i]) {
                  this.YData[i] = res.data.data[j].money;
                }
              }
            }
          }
        });
    },
    // 获取订单统计
    getOrderList() {
      this.$axios
        .get("/management/dataStatistics/maintenanceOrderCount")
        .then(res => {
          if (res.status == 200) {
            console.log(res.data.data);
            for (let i = 0; i < this.monthData2.length; i++) {
              for (let j = 0; j < res.data.data.length; j++) {
                if (res.data.data[j].timestr === this.monthData3[i]) {
                  this.YData2[i] = res.data.data[j].num;
                }
              }
            }
          }
        });
    },
    // 获取押金统计
    getDeposit() {
      this.$axios.get("/management/dataStatistics/depositCount").then(res => {
        if (res.status == 200) {
          console.log(res);
          if (res.data.code == 200) {
            console.log(res.data.data);
            this.YData3 = [
              { value: res.data.data.buyMoney, name: "已换购" },
              { value: res.data.data.deposit, name: "未提取" }
            ];
            // YData3[1].value/(YData3[1].value+YData3[0].value)*100).toFixed(2)
            this.returnMoneyPer = (res.data.data.deposit/(res.data.data.deposit+res.data.data.buyMoney)*100).toFixed(2) +"%"
            this.changeMoneyPer = (res.data.data.buyMoney/(res.data.data.deposit+res.data.data.buyMoney)*100).toFixed(2) +"%"
            this.returnMoney = res.data.data.deposit
            this.changeMoney = res.data.data.buyMoney
          }
        }
      });
    },
    // 初始化图表
    initChart1() {
      console.log(this.YData);
      this.chart1 = this.$echarts.init(this.$refs.salesEchart, "light");
      // 把配置和数据放这里
      this.chart1.setOption(
        {
          legend: {},
          tooltip: {},
          xAxis: {
            type: "category",
            data: this.monthData
          },
          yAxis: {
            type: "value",
            splitLine: {
                show: true,
                lineStyle:{
                    type:'dashed'
                }
            }
          },
          series: [
            {
              data: this.YData,
              type: "line"
            }
          ]
        },
        true
      );
    },
    initChart2() {
      console.log(this.YData2);
      this.chart2 = this.$echarts.init(this.$refs.orderEchart, "light");
      // 把配置和数据放这里
      this.chart2.setOption(
        {
          legend: {},
          tooltip: {},
          xAxis: {
            type: "category",
            data: this.monthData2
          },
          yAxis: {
            type: "value",
            splitLine: {
                show: true,
                lineStyle:{
                    type:'dashed'
                }
            }
          },
          series: [
            {
              data: this.YData2,
              type: "bar",
              barWidth: "30%"
            }
          ]
        },
        true
      );
    },
    initChart3() {
      this.chart3 = this.$echarts.init(this.$refs.depositEchart, "light");
      // 把配置和数据放这里
      this.chart3.setOption(
        {
          tooltip: {
            trigger: "item",
            formatter: "{a} <br/>{b}: {c} ({d}%)"
          },
          color: ["#FFC17C", "#7CA4FF"],
          legend: {
            orient: "vertical",
            x: "right",
            data: ["已换购", "未提取"]
          },
          graphic: [
            {
              //设置饼状图内部文字
              type: "group",
              left: "center", //设置偏移量
              top: "center",
              children: [
                {
                  type: "text",
                  left: "center", // 相对父元素居中
                  top: "middle", // 相对父元素居中
                  style: {
                    fill: "#000",
                    text: [
                      "押金总额",
                       " ￥" + (this.YData3[0].value + this.YData3[1].value)
                    ].join("\n"),
                    font: "16px Microsoft YaHei"
                  }
                }
              ]
            }
          ],
          series: [
            {
              name: "押金统计",
              type: "pie",
              radius: ["45%", "55%"],
              avoidLabelOverlap: false,
              label: {
                normal: {
                  show: false,
                  position: "center"
                },
                emphasis: {
                  show: false,
                  textStyle: {
                    fontSize: "30",
                    fontWeight: "bold"
                  }
                }
              },
              labelLine: {
                normal: {
                  show: false
                }
              },
              data: this.YData3
            }
          ]
        },
        true
      );
    },
    //获取销售额排名
    getsaleRank(type) {
      if (type == 4) {
        this.$axios
          .post(
            `/management/dataStatistics/selectOrderByShop?type=4`,
            this.$qs.stringify({
              startDate: this.value.length ? this.value[0] : "",
              endDate: this.value.length ? this.value[1] : ""
            })
          )
          .then(res => {
            if (res.status == 200) {
              console.log(res);
              this.saleData = res.data.data;
            }
          });
        return;
      }
      this.$axios
        .post(`/management/dataStatistics/selectOrderByShop?type=${type}`)
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.saleData = res.data.data;
            this.value = [];
          }
        });
    },
    // 获取商家反馈排名统计
    getfeedback() {
      this.$axios
        .get("/management/dataStatistics/countProblemByType")
        .then(res => {
          if (res.status == 200) {
            console.log(res);
            this.feedbackData = res.data.data;
          }
        });
    }
  }
};
</script>

<style  scoped>
* {
  text-decoration: none;
  list-style: none;
}
.content{
  min-height: 10px;
}
.salesEchart {
  /* position: relative; */
  width: 100%;
  height: 300px;
}
.orderEchart {
  width: 100%;
  height: 280px;
}
.depositEchart {
  width: 100%;
  height: 230px;
}
.text {
  font-size: 14px;
}

.item {
  padding: 18px 0;
}
.timepick {
  text-align: right;
  position: relative;
  top: -9px;
}
.sale-rank {
  height: 300px;
  padding: 20px;
  box-sizing: border-box;
}
.sale-rank li {
  margin-top: 15px;
}
.cicle-box {
  width: 20px;
  height: 20px;
  /* border-radius: 50%; */
  /* background-color: #314659; */
  text-align: center;
  color: aliceblue;
}
.feedback-rank {
  padding: 20px;
  box-sizing: border-box;
  height: 280px;
}
.feedback-rank li {
  margin-top: 15px;
}
.cicle-box-1 {
  background-image: url("../assets/images/1.png");
}
.cicle-box-2 {
  background-image: url("../assets/images/2.png");
}
.cicle-box-3 {
  background-image: url("../assets/images/3.png");
}
.cicle-box-last {
  color: #b4c6de;
}
.cicle {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background-color: #606266;
}
.cicle-1 {
  background-color: #f7aa68;
}
.cicle-2 {
  background-color: #f6d91e;
}
.cicle-3 {
  background-color: #7ca4ff;
}
/* .notfound {
  background-image: url("../assets/images/404.png");
  background-size: 50%;
  height: 100%;
} */
.sale-title {
  /* line-height: 40px; */
  font-family: PingFangSC-Regular;
  font-size: 14px;
  color: #7ca4ff;
  letter-spacing: 0;
}
.box-card {
  margin-bottom: 20px;
  position: relative;
  border-radius: 8px;
}
.tip-title {
  position: absolute;
  top: -1px;
  left: -1px;
  padding: 10px 20px;
  background: #7ca4ff;
  border-radius: 8px 0px 0px 0px;
  color: azure;
}
.left {
  padding-right: 20px;
}
.msg-footer{
  height: 50px;
}
.point-1{
  display: inline-block;
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background: #7CA4FF;
  margin-right: 5px;
}
.point-2{
  display: inline-block;
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background: #FFC17C;
  margin-right: 5px;
}
</style>