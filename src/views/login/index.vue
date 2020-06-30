<template>
  <div class="login">
    <div class="login-warp">
      <ul class="menu-tab">
        <li
          :class="{'current':item.current}"
          v-for="(item, index) in menuTab"
          :key="index"
          @click="toggleMenu(item)"
        >{{item.txt}}</li>
      </ul>
      <el-form
        :model="ruleForm"
        status-icon
        :rules="rules"
        ref="ruleForm"
        class="login-form"
        size="medium"
      >
        <el-form-item prop="username" class="item-form">
          <label>邮箱</label>
          <el-input type="text" v-model="ruleForm.username" autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item prop="password" class="item-form">
          <label>密码</label>
          <el-input
            type="text"
            v-model="ruleForm.password"
            autocomplete="off"
            maxlength="20"
            minlength="6"
          ></el-input>
        </el-form-item>

        <el-form-item prop="passwords" class="item-form" v-if="model === 'register'">
          <label>再次输入密码</label>
          <el-input
            type="text"
            v-model="ruleForm.passwords"
            autocomplete="off"
            maxlength="20"
            minlength="6"
          ></el-input>
        </el-form-item>

        <el-form-item prop="code" class="item-form">
          <label>验证码</label>
          <el-row :gutter="20">
            <el-col :span="15">
              <el-input v-model="ruleForm.code" maxlength="6"></el-input>
            </el-col>
            <el-col :span="9">
              <el-button type="success">获取验证码</el-button>
            </el-col>
          </el-row>
        </el-form-item>

        <el-form-item>
          <el-button type="danger" class="login-btn block" @click="submitForm('ruleForm')">提交</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>
<script>
import {
  stripscript,
  validateEmail,
  validatePass,
  validateVCode
} from "@/utils/validate.js";
export default {
  name: "login",
  data() {
    // 验证用户名为邮箱
    let validateUsername = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入用户名"));
      } else if (!validateEmail(value)) {
        callback(new Error("用户名格式有误"));
      } else {
        callback(); //true
      }
    };
    // 验证密码
    var validatePassword = (rule, value, callback) => {
      this.ruleForm.password = stripscript(value);
      value = this.ruleForm.password;
      if (value === "") {
        callback(new Error("请输入密码"));
      } else if (!validatePass(value)) {
        callback(new Error("密码为6到20位的字母加数字"));
      } else {
        callback();
      }
    };
    // 再次验证密码
    var validatePasswords = (rule, value, callback) => {
      this.ruleForm.passwords = stripscript(value);
      value = this.ruleForm.passwords;
      if (value === "") {
        callback(new Error("请再次输入密码"));
      } else if (value !== this.ruleForm.password) {
        callback(new Error("重复密码不同"));
      } else {
        callback();
      }
    };
    // 验证码
    let validateCode = (rule, value, callback) => {
      this.ruleForm.code = stripscript(value);
      value = this.ruleForm.code;
      if (value === "") {
        return callback(new Error("请输入验证码"));
      } else if (!validateVCode(value)) {
        return callback(new Error("验证码格式有误"));
      } else {
        callback();
      }
    };
    return {
      menuTab: [
        { txt: "登录", current: true, type: "login" },
        { txt: "注册", current: false, type: "register" }
      ],
      model: "login",
      // 输入的数据双向绑定
      ruleForm: {
        username: "",
        password: "",
        passwords: "",
        code: ""
      },
      // 验证的数据
      rules: {
        username: [{ validator: validateUsername, trigger: "blur" }], //blur 失去焦点时触发
        password: [{ validator: validatePassword, trigger: "blur" }],
        passwords: [{ validator: validatePasswords, trigger: "blur" }],
        code: [{ validator: validateCode, trigger: "blur" }]
      }
    };
  },

  methods: {
    // 注册和登录点击事件
    toggleMenu(data) {
      // 点击切换样式
      this.menuTab.forEach(e => {
        e.current = false;
      });
      data.current = true;
      // 点击切换登录/注册模块
      this.model = data.type;
    },
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          alert("submit!");
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    }
  }
};
</script>
<style lang="scss" scoped>
.login {
  background: #344a5f;
  height: 100vh;
  &-warp {
    position: absolute;
    top: 50%;
    width: 330px;
    margin: auto;
    left: 50%;
    transform: translate(-50%, -50%);
    .menu-tab {
      text-align: center;
      li {
        display: inline-block;
        font-size: 14px;
        color: #fff;
        width: 88px;
        height: 36px;
        line-height: 36px;
        border-radius: 2px;
        cursor: pointer;
      }
      .current {
        background: #2f4255;
      }
    }
  }
  &-form {
    margin-top: 29px;
    label {
      color: #fff;
      font-size: 14px;
      margin-bottom: 3px;
    }
    .item-form {
      margin-top: 13px;
    }
    .block {
      width: 100%;
    }
    .login-btn {
      margin-top: 19px;
    }
  }
}
</style>
