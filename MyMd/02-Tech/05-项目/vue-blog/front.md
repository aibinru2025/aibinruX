# front

## App.vue

```vue
<template>
  <div class="login-container">
    <div class="login-box">
      <h2>Welcome Back</h2>
      <form @submit.prevent="handleSubmit">
        <div class="input-group">
          <label for="username">Username</label>
          <input v-model="username" type="text" id="username" placeholder="Enter your username" required />
        </div>

        <div class="input-group">
          <label for="password">Password</label>
          <input v-model="password" type="password" id="password" placeholder="Enter your password" required />
        </div>

        <div class="input-group">
          <label for="captcha">Captcha</label>
          <div class="captcha-input">
            <input v-model="captcha" type="text" id="captcha" placeholder="Enter captcha" required />
            <img :src="captchaUrl" alt="Captcha" @click="refreshCaptcha" />
          </div>
        </div>

        <button type="submit" class="login-btn">Login</button>
        <p class="forgot-password" @click="handleForgotPassword">Forgot your password?</p>
      </form>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      username: '',
      password: '',
      captcha: '',
      captchaUrl: '' // 用来存储验证码图片的 URL
    }
  },
  mounted() {
    this.loadCaptcha() // 页面加载时获取验证码
  },
  methods: {
    // 加载验证码
    loadCaptcha() {
      axios.get('/api/get-captcha') // 通过 GET 请求获取验证码
        .then(response => {
          this.captchaUrl = response.data.captchaUrl // 设置验证码的图片链接
        })
        .catch(error => {
          console.error('Error fetching captcha:', error)
        })
    },

    // 刷新验证码
    refreshCaptcha() {
      this.loadCaptcha() // 重新加载验证码
    },

    // 处理登录
    handleSubmit() {
      const data = {
        username: this.username,
        password: this.password,
        captcha: this.captcha
      }

      axios.post('/api/login', data) // POST 请求进行登录
        .then(response => {
          if (response.data.success) {
            alert('Login successful!')
          } else {
            alert('Login failed: ' + response.data.message)
          }
        })
        .catch(error => {
          console.error('Login error:', error)
        })
    },

    // 处理忘记密码
    handleForgotPassword() {
      const email = prompt("Enter your email to reset password:")
      if (email) {
        axios.post('/api/forgot-password', { email })
          .then(response => {
            alert('Password reset instructions have been sent to your email.')
          })
          .catch(error => {
            console.error('Forgot password error:', error)
          })
      }
    }
  }
}
</script>

<style scoped>
/* Your styles here (same as before with minor adjustments) */
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-image: url(''@/assets/img/English.jpg');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
}

.login-box {
  width: 350px;
  padding: 40px;
  background-color: rgba(255, 255, 255, 0.9);
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
  text-align: center;
}

h2 {
  margin-bottom: 30px;
  font-size: 26px;
  color: #333;
}

.input-group {
  margin-bottom: 20px;
  text-align: left;
}

.input-group label {
  font-size: 14px;
  color: #555;
  display: block;
  margin-bottom: 8px;
}

.input-group input {
  width: 100%;
  padding: 12px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 16px;
  background-color: #f9f9f9;
}

.input-group input:focus {
  border-color: #0056b3;
  outline: none;
}

.captcha-input {
  display: flex;
  align-items: center;
}

.captcha-input input {
  width: 60%;
}

.captcha-input img {
  margin-left: 10px;
  cursor: pointer;
  width: 100px;
  height: 40px;
}

.login-btn {
  width: 100%;
  padding: 12px;
  background-color: #0056b3;
  color: white;
  border: none;
  border-radius: 6px;
  font-size: 16px;
  cursor: pointer;
}

.login-btn:hover {
  background-color: #00408d;
}

.forgot-password {
  margin-top: 20px;
  font-size: 14px;
  color: #0056b3;
  cursor: pointer;
}

.forgot-password:hover {
  text-decoration: underline;
}
</style>

```





