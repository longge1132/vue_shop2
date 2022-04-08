<template>
  <div class="login_container">
    <div class="login_box">
      <img class="head" src="../assets/imgs/square2.png" alt="#" />
      <el-form
        class="login_form"
        ref="loginFormRef"
        :rules="rules"
        :model="loginForm"
        label-width="60px"
      >
        <el-form-item label="用户:" prop="username">
          <el-input
            prefix-icon="el-icon-user-solid"
            v-model="loginForm.username"
          ></el-input>
        </el-form-item>

        <el-form-item label="密码:" prop="password">
          <el-input
            prefix-icon="el-icon-s-opportunity"
            v-model="loginForm.password"
            type="password"
          ></el-input>
        </el-form-item>
        <el-form-item class="btn">
          <el-button type="primary" @click="onSubmit">登录</el-button>
          <el-button type="warning" @click="reSet">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      loginForm: {
        username: 'admin',
        password: '123456',
      },
      rules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          {
            min: 3,
            max: 15,
            message: '长度在 3 到 15 个字符',
            trigger: 'blur',
          },
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 6, max: 15, message: '长度在 3 到 5 个字符', trigger: 'blur' },
        ],
      },
    }
  },
  methods: {
    onSubmit() {
      this.$refs.loginFormRef.validate(async (valid) => {
        if (!valid) return false
        const { data: res } = await this.$http.post('/login', this.loginForm)
        if (res.meta.status !== 200) return this.$message.error('登陆失败！')
        this.$message.success('登陆成功')
        window.sessionStorage.setItem('token', res.data.token)
        this.$router.push('/home')
      })
    },
    reSet() {
      this.$refs.loginFormRef.resetFields()
    },
  },
}
</script>

<style lang="less" scoped>
.login_container {
  height: 100%;
  background-color: bisque;
}

.login_box {
  width: 450px;
  height: 300px;
  border-radius: 10px;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #fff;
}

.head {
  width: 120px;
  height: 120px;
  border: 1px solid #eee;
  border-radius: 50%;
  position: absolute;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 10px;
  box-shadow: 0 0 6px #ddd;
  background-color: #fff;
}

.login_form {
  position: absolute;
  bottom: 10%;
  width: 100%;
  padding-right: 30px;
  box-sizing: border-box;
}

.btn {
  display: flex;
  justify-content: flex-end;
}
</style>
