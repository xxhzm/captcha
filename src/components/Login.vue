<script setup>
import { reactive, ref } from 'vue'
import axios from 'axios'

// 表单信息
const info = reactive({
  username: '',
  password: '',
  code: '',
})

// 验证码信息
const captchaInfo = ref({
  id: '',
  url: ''
})

// 获取验证码的 id 跟图片地址
const captchaChange = async () => {
  // 接口文档 https://api-m.com/doc/captcha 
  const { data: res } = await axios.get('https://v2.api-m.com/api/captcha?type=digit')
  captchaInfo.value = res.data
}

captchaChange()

// 登录函数
const login = async () => {
  if (info.username === '' || info.password === '' || info.code === '') {
    return false
  }

  // 判断验证码是否正确
  const { data: res } = await axios.get(`https://v2.api-m.com/api/captcha?id=${captchaInfo.value.id}&key=${info.code}`)
  if (res.code === -2 || res.msg === '验证失败') {
    alert('验证码错误')
    captchaChange()
  } else if (res.code === 200 && res.data === '验证成功') {
    alert('验证码正确')
  }

  // 登录正常逻辑……
}

</script>

<template>
  <div class="login-container">
    <div class="login cont">
      <h4 class="title">登录</h4>
      <p class="text">请输入账号密码进行登录</p>
      <el-form
        label-position="top"
        label-width="100px"
        :model="info"
        size="large"
      >
        <el-form-item>
          <el-input v-model="info.username" placeholder="用户名" />
        </el-form-item>
        <el-form-item>
          <el-input
            v-model="info.password"
            type="password"
            placeholder="密码"
            show-password
          />
        </el-form-item>
        <el-form-item>
          <img :src="captchaInfo.url" alt="" @click="captchaChange" />
        </el-form-item>
        <el-form-item>
          <el-input v-model="info.code" placeholder="验证码" />
        </el-form-item>
      </el-form>
      <el-button
        type="primary"
        style="display: block; margin-top: 30px"
        @click="login"
        >点击登录</el-button
      >
    </div>
  </div>
</template>

<style lang="less" scoped>
.login-container {
  position: relative;
  width: 100%;
  height: 100vh;
  background: #f3f4f6;
  .cont {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    width: 420px;
    height: auto;
    background: #fff;
    padding: 2.25rem;
    text-align: center;
    box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.05);
    .back {
      float: left;
      font-size: 13px;
      font-weight: bold;
      color: #98a6ad;
      cursor: pointer;
      margin-bottom: 48px;
      svg {
        display: inline-block;
        position: relative;
        top: 2px;
        width: 13px;
        margin-right: 2px;
      }
    }
    .title {
      clear: both;
      font-weight: 700;
      font-size: 1.125rem;
      color: #585a63;
    }
    .text {
      color: #98a6ad;
      font-size: 0.9rem;
      margin: 12px 0 16px 0;
    }
    .tips {
      float: left;
      color: #98a6ad;
      font-weight: 700;
      font-size: 0.9rem;
    }
    button {
      width: 100%;
      height: 40px;
      clear: both;
    }
    .text {
      span {
        cursor: pointer;
        color: #0084ff;
      }
    }
  }
}
</style>
