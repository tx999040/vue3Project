<template>
  <div class="login-container">
    <div class="login-card">
      <div class="login-header">
        <h1 class="login-title">光伏系统登录</h1>
      </div>

      <el-form
        ref="loginFormRef"
        :model="loginForm"
        :rules="loginRules"
        class="login-form"
        label-position="right"
        label-width="auto"
        style="max-width: 500px"
        @keyup.enter="handleLogin"
      >
        <el-form-item prop="username" label="用户名">
          <el-input
            v-model="loginForm.username"
            placeholder="请输入用户名"
            prefix-icon="User"
            clearable
          />
        </el-form-item>

        <el-form-item prop="password" label="密码">
          <el-input
            v-model="loginForm.password"
            placeholder="请输入密码"
            prefix-icon="Lock"
            show-password
            clearable
          />
        </el-form-item>

        <el-form-item prop="captcha" label="验证码">
          <div class="captcha-container">
            <el-input
              v-model="loginForm.captcha"
              placeholder="请输入验证码"
              prefix-icon="Key"
              class="captcha-input"
              clearable
            />
            <div class="captcha-image" @click="refreshCaptcha">
              <img :src="captchaUrl" alt="验证码" />
            </div>
          </div>
        </el-form-item>

        <el-form-item>
          <el-checkbox v-model="rememberMe">记住我</el-checkbox>
          <a class="forget-password">忘记密码?</a>
        </el-form-item>

        <el-form-item>
          <el-button :loading="loading" type="primary" class="login-button" @click="handleLogin">
            {{ loading ? '登录中...' : '登录' }}
          </el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, reactive, onMounted } from 'vue'
import { ElMessage } from 'element-plus'
import type { FormInstance, FormRules } from 'element-plus'
import LeftTable from 'element-plus/es/components/table-v2/src/renderers/left-table.mjs'

// 登录表单引用
const loginFormRef = ref<FormInstance>()

// 登录表单数据
const loginForm = reactive({
  username: '',
  password: '',
  captcha: '',
})

// 记住我选项
const rememberMe = ref(false)

// 加载状态
const loading = ref(false)

// 验证码URL
const captchaUrl = ref('/api/captcha?t=' + new Date().getTime())

// 表单验证规则
const loginRules = reactive<FormRules>({
  username: [
    { required: true, message: '请输入用户名', trigger: 'blur' },
    { min: 3, max: 20, message: '用户名长度应在3-20个字符之间', trigger: 'blur' },
  ],
  password: [
    { required: true, message: '请输入密码', trigger: 'blur' },
    { min: 6, max: 20, message: '密码长度应在6-20个字符之间', trigger: 'blur' },
  ],
  captcha: [
    { required: true, message: '请输入验证码', trigger: 'blur' },
    { min: 4, max: 6, message: '请输入正确的验证码', trigger: 'blur' },
  ],
})

// 刷新验证码
const refreshCaptcha = () => {
  captchaUrl.value = '/api/captcha?t=' + new Date().getTime()
}

// 登录处理
const handleLogin = async () => {
  if (!loginFormRef.value) return

  await loginFormRef.value.validate((valid, fields) => {
    if (valid) {
      loading.value = true

      // 模拟登录请求
      setTimeout(() => {
        loading.value = false
        ElMessage.success('登录成功')
        // 实际项目中这里应该调用API进行登录验证
        // 登录成功后重定向到系统首页
      }, 1500)
    } else {
      console.error('登录验证失败', fields)
    }
  })
}

// 组件挂载时刷新验证码
onMounted(() => {
  refreshCaptcha()
})
</script>

<style scoped>
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #f5f7fa;
}

.login-card {
  width: 400px;
  padding: 30px;
  background-color: white;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}

.login-header {
  text-align: center;
  margin-bottom: 30px;
}

.login-title {
  font-size: 24px;
  color: #303133;
  margin: 0;
}

.login-form {
  margin-top: 20px;
}

.captcha-container {
  display: flex;
  align-items: center;
}

.captcha-input {
  flex: 1;
}

.captcha-image {
  margin-left: 10px;
  height: 40px;
  width: 120px;
  cursor: pointer;
  border-radius: 4px;
  overflow: hidden;
}

.captcha-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.login-button {
  width: 100%;
}

.forget-password {
  float: right;
  color: #409eff;
  cursor: pointer;
}
</style>
