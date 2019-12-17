<template>
  <div class="login-wrap">
    <img src="../../assets/images/bg2.png" alt="" class="bg2">
    <div class="ms-login">
      <img
        src="../../assets/images/logo.png"
        alt
      >
      <div class="login-box">
        <div class="ms-title">充电宝管理系统</div>
        <el-form
          :model="ruleForm"
          :rules="rules"
          ref="ruleForm"
          class="ms-content"
        >
          <el-form-item
            prop="username"
            label="账号"
            size="small"
          >
            <el-input
              v-model="ruleForm.username"
              placeholder="username"
              @keyup.enter.native="submitForm('ruleForm')"
            ></el-input>
          </el-form-item>
          <el-form-item
            prop="password"
            label="密码"
            class="password"
            size="small"
          >
            <el-input
              type="password"
              placeholder="password"
              v-model="ruleForm.password"
              @keyup.enter.native="submitForm('ruleForm')"
            ></el-input>
            <el-checkbox v-model="checked">记住密码</el-checkbox>
          </el-form-item>

          <!-- <div class="username-rem">
            <span>
              <el-checkbox v-model="checked">备选项</el-checkbox>
            </span>
            <span>忘记密码?</span>
          </div> -->
          <div class="login-btn">
            <el-button
              type="primary"
              @click="submitForm('ruleForm')"
            >登录</el-button>
          </div>
        </el-form>
      </div>
    </div>
  </div>
</template>

<script>
// import store from "../../vuex/vuex.js";
export default {
  data: function() {
    return {
      checked: true,
      logo: "./static/img/logo.png",
      ruleForm: {
        username: "",
        password: ""
      },
      rules: {
        username: [
          { required: true, message: "请输入用户名", trigger: "blur" }
        ],
        password: [{ required: true, message: "请输入密码", trigger: "blur" }]
      }
    };
  },
  mounted () {
    this.getCookie();
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$axios
            .post(
              "/management/login/doLogin",
              this.$qs.stringify({
                loginName: this.ruleForm.username,
                password: this.ruleForm.password
              })
            )
            .then(res => {
              console.log(res);
              if (res.status == 200) {
                if (res.data.code == 200) {
                  sessionStorage.setItem("token", res.data.data.authStr);
                  sessionStorage.setItem("userName", res.data.data.name);
                  sessionStorage.setItem("id", res.data.data.userId);
                  sessionStorage.setItem("permissionList", JSON.stringify(res.data.data.permissionList));
                  console.log(111);
                  this.$router.push("/");
                  if (this.checked) {
                    this.setCookie(
                      this.ruleForm.username,
                      this.ruleForm.password,
                      30
                    );
                  } else {
                    this.clearCookie();
                  }
                } else {
                  this.$message.error("账号或密码错误!");
                }
              } else {
                this.$message.error("服务器链接失败");
              }
            });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    //设置cookie
    setCookie(c_name, c_pwd, exdays) {
      var exdate = new Date(); //获取时间
      exdate.setTime(exdate.getTime() + 24 * 60 * 60 * 1000 * exdays); //保存的天数
      //字符串拼接cookie
      window.document.cookie =
        "userName" + "=" + c_name + ";path=/;expires=" + exdate.toGMTString();
      window.document.cookie =
        "userPwd" + "=" + c_pwd + ";path=/;expires=" + exdate.toGMTString();
    },
    //读取cookie
    getCookie: function() {
      if (document.cookie.length > 0) {
        var arr = document.cookie.split("; "); //这里显示的格式需要切割一下自己可输出看下
        for (var i = 0; i < arr.length; i++) {
          var arr2 = arr[i].split("="); //再次切割
          //判断查找相对应的值
          if (arr2[0] == "userName") {
            this.ruleForm.username = arr2[1]; //保存到保存数据的地方
          } else if (arr2[0] == "userPwd") {
            this.ruleForm.password = arr2[1];
          }
        }
      }
    },
    //清除cookie
    clearCookie: function() {
      this.setCookie("", "", -1); //修改2值都为空，天数为负1天就好了
    }
  }
};
</script>

<style>
.login-wrap {
  position: relative;
  width: 100%;
  height: 100%;
  background: url("../../assets/images/bg.png") no-repeat;
}
.bg2{
  position: absolute;
  width:600px;
left: 10%;
top: 20%;
}
.login-box {
  border-radius: 5px;
  box-shadow:0px 4px 4px 0px rgba(31, 138, 61, 0.35);
  overflow: hidden;
  background: #fff;
}
.ms-title {
  width: 100%;
  line-height: 50px;
  text-align: center;
  font-size: 20px;
  margin-top: 16px;
  color: #303133;
}
.ms-login {
  position: absolute;
  right: 17%;
  top: 48%;
  transform: translateY(-50%);
  width: 365px;
  border-radius: 5px;
}
.ms-login img {
  width: 240px;
  display: block;
  margin-bottom: 40px;
  margin-left: 70px;
}
.ms-content {
  padding: 16px 30px;
}
.login-btn {
  text-align: center;
}
.login-btn button {
  width: 100%;
  height: 36px;
  margin-bottom: 10px;
  background-color: #7ca4ff;
  border-color: #7ca4ff;
  margin-bottom: 16px;
}
.ms-login .el-form-item__label {
  color: #c0c4cc !important;
}
.ms-login .el-input__inner:focus,
.ms-login .el-input__inner {
  border-color: #b8cdfd;
}
.password {
  margin-top: -15px !important;
  margin-bottom: 30px !important;
}
.login-tips {
  font-size: 12px;
  line-height: 30px;
  color: #fff;
}
.username-rem {
  display: flex;
  justify-content: space-between;
}
.username-rem .el-checkbox__label {
  padding: 0;
}
.el-checkbox__inner {
  border-radius: 50%;
}
</style>