<template>
  <div class="login-page fillcontain">
    <h1 class="title">{{ APP_NAME }}</h1>

    <!-- 登陆 -->
    <div class="login-panel">
      <h2>登陆</h2>
      <el-form-renderer :content="loginContent" ref="form">
        <el-form-item label=" ">
          <el-button
            type="primary"
            class="login-btn"
            :loading="loading"
            @click="login"
            >登陆</el-button
          >
        </el-form-item>
      </el-form-renderer>
    </div>
  </div>
</template>

<script>
import { value, onMounted, onBeforeDestroy } from "vue-function-api";

import { APP_NAME } from "@/common/const";

import { debounce } from "@/common/utils";

const loginForm = [
  // {
  //   type: "input",
  //   id: "username",
  //   el: {
  //     placeholder: "用户名",
  //     prefixIcon: "el-icon-user"
  //   },
  //   rules: [
  //     {
  //       trigger: "blur",
  //       required: true,
  //       message: "请填写用户名"
  //     }
  //   ]
  // },
  {
    type: "input",
    id: "access_token",
    el: {
      placeholder: "github token",
      prefixIcon: "el-icon-lock",
      type: "password"
    },
    rules: [
      {
        trigger: "blur",
        required: true,
        message: "请填写 github token"
      }
    ]
  }
];

export default {
  setup(props, ctx) {
    const loginContent = value(loginForm);
    const loading = value(false);
    const { $store, toast, $router } = ctx.root;

    const login = () => {
      const { form } = ctx.refs;

      form.validate(validate => {
        if (!validate) return;

        const data = form.getFormValue();
        loading.value = true;
        $store
          .dispatch("user/login", data)
          .then(() => {
            toast("登录成功");
            $router.push("/");
          })
          .catch(() => {
            toast("出现了点小错误，客观请充实一下~", "error");
          })
          .finally(() => {
            loading.value = false;
          });
      });
    };

    const handleListenerEnter = debounce(event => {
      if (event.key !== "Enter") return;
      login();
    });

    onMounted(() => {
      window.addEventListener("keyup", handleListenerEnter);
    });

    onBeforeDestroy(() => {
      window.removeEventListener("keyup", handleListenerEnter);
    });

    return {
      loginContent,
      login,
      loading,
      APP_NAME
    };
  }
};
</script>

<style lang="less">
.login-page {
  background: #324057;
  color: #fff;

  .title {
    position: absolute;
    top: 20%;
    left: 50%;
    transform: translate(-50%, 0);
    font-size: 36px;
    font-weight: 700;
  }
  .login-panel,
  .register-panel {
    position: absolute;
    background: #fff;
    padding: 40px;
    border-radius: 10px;
    left: 50%;
    top: 30%;
    transform: translate(-50%, 0);
    width: 400px;

    > h2 {
      color: #324057;
      font-size: 24px;
      font-weight: 700;
      margin-bottom: 20px;
    }
    .el-button + .el-button {
      margin-left: 20px;
    }
  }

  .login-btn {
    width: 100%;
  }
  .reset-main {
    text-align: right;
  }
}
</style>
