<template>
  <v-app id="inspire">
    <v-content>
      <v-container class="fill-height" fluid>
        <v-row align="center" justify="center">
          <v-col cols="12" sm="8" md="4">
            <v-card class="elevation-12">
              <v-toolbar color="primary" dark flat>
                <v-toolbar-title>登录</v-toolbar-title>
                <div class="flex-grow-1"></div>
                <v-tooltip bottom>
                  <template v-slot:activator="{ on }">
                    <v-btn class="mr-3" href="/register" icon large target="_blank" v-on="on">
                      注册
                      <v-icon>account-plus</v-icon>
                    </v-btn>
                  </template>
                  <span>前往注册</span>
                </v-tooltip>
                <v-tooltip right>
                  <template v-slot:activator="{ on }">
                    <v-btn icon large href="/" target="_blank" v-on="on">
                      <v-icon>home</v-icon>
                    </v-btn>
                  </template>
                  <span>前往首页</span>
                </v-tooltip>
              </v-toolbar>
              <v-card-text>
                <v-form>
                  <v-text-field
                    label="用户名 / 邮箱 / 手机"
                    name="login"
                    type="text"
                    v-model="loginAccount"
                  ></v-text-field>

                  <v-text-field
                    id="password"
                    label="密码"
                    name="password"
                    type="password"
                    v-model="password"
                  ></v-text-field>
                </v-form>
              </v-card-text>
              <v-card-actions>
                <div class="flex-grow-1"></div>
                <v-btn color="primary" @click="login">登录</v-btn>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-content>
    <v-snackbar v-model="showTipMsg">
      {{ tipMsg }}
      <v-btn color="pink" text @click="showTipMsg = false">Close</v-btn>
    </v-snackbar>
  </v-app>
</template>

<script>
import { isLoginMethod } from "./../../utils/index";

export default {
  metaInfo: {
    title: "登录 - remember"
  },
  data() {
    return {
      loginAccount: "",
      password: "",
      showTipMsg: false,
      tipMsg: ""
    };
  },
  created() {
    this.isLoginRedirect();
  },
  methods: {
    login() {
      this.$http({
        method: "post",
        url: "/api/User/Login",
        data: {
          loginAccount: this.loginAccount,
          password: this.password
        }
      }).then(res => {
        if (res.data.code > 0) {
          // 登录成功
          // 存入 localStorage
          localStorage.token = res.data.data.token;
          localStorage.token_expire = res.data.data.expire;

          // 存入 vuex
          // this.$store.commit('saveToken', res.data.data.token, res.data.data.expire);
          this.$store.commit("getUser", this);

          // 跳转页面
          if (!!this.$route.query.redirect) {
            // TODO: 跳转到之前页
            // history模式
            // window.location.href = this.$route.query.redirect;
            // 失败
            // window.location.href =
            // window.location.origin + "/#" + this.$route.query.redirect;
            // this.$router.push({ path: this.$route.query.redirect });
            this.$router.push({ name: "Home" });
          } else {
            this.$router.push({ name: "Home" });
          }
        } else {
          // 登录失败
          this.tipMsg = res.data.message;
          this.showTipMsg = true;
        }
      });
    },
    isLoginRedirect() {
      if (isLoginMethod()) {
        // 跳转页面
        if (!!this.$route.query.redirect) {
          // TODO: 跳转到之前页
          // history模式
          // window.location.href = this.$route.query.redirect;
          // 以下失败
          // window.location.href =
          // window.location.origin + "/#" + this.$route.query.redirect;
          // console.log(this.$route.query.redirect);
          // this.$router.push({ path: this.$route.query.redirect });
          this.$router.push({ name: "Home" });
        } else {
          this.$router.push({ name: "Home" });
        }
      }
    }
  }
};
</script>