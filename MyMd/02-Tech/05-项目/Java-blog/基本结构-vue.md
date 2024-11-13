## 基本结构

为了实现一个简洁的博客前端页面，你可以先搭建一个基础的 Vue 前端应用，暂时不涉及后端，直接实现文章发布和浏览功能，以及登录页面的跳转。

### 1. **登录页面修改**

首先，你的登录页面可以保持简洁，点击登录后直接跳转到博客主页页面（不涉及后端验证）。

#### 登录页面（`Login.vue`）

```vue
<template>
  <div class="login-container">
    <h2>登录</h2>
    <form @submit.prevent="handleLogin">
      <div class="input-group">
        <input v-model="username" type="text" placeholder="用户名" required />
      </div>
      <div class="input-group">
        <input v-model="password" type="password" placeholder="密码" required />
      </div>
      <button type="submit">登录</button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      username: '',
      password: ''
    };
  },
  methods: {
    handleLogin() {
      // 模拟登录成功后跳转到博客主页
      this.$router.push('/blog');
    }
  }
};
</script>

<style scoped>
.login-container {
  width: 100%;
  max-width: 400px;
  margin: 50px auto;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
  background-color: #f9f9f9;
}
h2 {
  text-align: center;
  margin-bottom: 20px;
}
.input-group {
  margin-bottom: 15px;
}
input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
}
button {
  width: 100%;
  padding: 10px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 16px;
  cursor: pointer;
}
button:hover {
  background-color: #358a68;
}
</style>
```

这里模拟登录成功后，点击 "登录" 按钮会跳转到 `/blog` 页面。

#### 旧

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
  background-image: url('@/assets/img/English.jpg');
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

#### 样式

```css


<style scoped>
/* Your styles here (same as before with minor adjustments) */
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-image: url('@/assets/img/English.jpg');
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



### 2. **博客主页（`Blog.vue`）**

接下来，我们可以创建一个简单的博客主页，允许用户查看文章和发布文章。

#### 博客主页（`Blog.vue`）

```vue
<template>
  <div class="blog-container">
    <h2>我的博客</h2>
    <div class="articles">
      <div class="article" v-for="article in articles" :key="article.id">
        <h3>{{ article.title }}</h3>
        <p>{{ article.content }}</p>
        <button @click="viewArticle(article)">查看全文</button>
      </div>
    </div>
    <button @click="createArticle">发布新文章</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      articles: [
        { id: 1, title: "文章 1", content: "这是文章 1 的简短内容" },
        { id: 2, title: "文章 2", content: "这是文章 2 的简短内容" },
      ]
    };
  },
  methods: {
    viewArticle(article) {
      // 跳转到查看文章页面
      alert(`查看文章: ${article.title}`);
    },
    createArticle() {
      // 跳转到发布新文章页面
      alert("跳转到发布新文章页面");
    }
  }
};
</script>

<style scoped>
.blog-container {
  width: 100%;
  max-width: 800px;
  margin: 50px auto;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
  background-color: #f9f9f9;
}
h2 {
  text-align: center;
  margin-bottom: 20px;
}
.articles {
  margin-bottom: 20px;
}
.article {
  border-bottom: 1px solid #ddd;
  margin-bottom: 15px;
  padding-bottom: 15px;
}
h3 {
  font-size: 20px;
  color: #333;
}
button {
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 8px 16px;
  cursor: pointer;
  margin-top: 10px;
}
button:hover {
  background-color: #358a68;
}
</style>
```

### 3. **创建文章页面（`CreateArticle.vue`）**

你可以实现一个简单的页面，允许发布文章。点击 "发布新文章" 后跳转到该页面，在该页面创建一个新的文章。

#### 创建文章页面（`CreateArticle.vue`）

```vue
<template>
  <div class="create-article-container">
    <h2>发布新文章</h2>
    <form @submit.prevent="submitArticle">
      <div class="input-group">
        <input v-model="title" type="text" placeholder="文章标题" required />
      </div>
      <div class="input-group">
        <textarea v-model="content" placeholder="文章内容" rows="6" required></textarea>
      </div>
      <button type="submit">发布</button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      title: '',
      content: ''
    };
  },
  methods: {
    submitArticle() {
      alert("文章已发布！标题：" + this.title);
      // 这里模拟发布文章后跳转到博客首页
      this.$router.push('/blog');
    }
  }
};
</script>

<style scoped>
.create-article-container {
  width: 100%;
  max-width: 800px;
  margin: 50px auto;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
  background-color: #f9f9f9;
}
h2 {
  text-align: center;
  margin-bottom: 20px;
}
.input-group {
  margin-bottom: 15px;
}
input, textarea {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
}
button {
  width: 100%;
  padding: 10px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 16px;
  cursor: pointer;
}
button:hover {
  background-color: #358a68;
}
</style>
```

### 4. **路由配置（`router/index.js`）**

你需要在 Vue 路由中配置这些页面，以便用户可以在不同的页面间跳转。

```javascript
import { createRouter, createWebHistory } from 'vue-router';
import Login from '../views/Login.vue';
import Blog from '../views/Blog.vue';
import CreateArticle from '../views/CreateArticle.vue';

const routes = [
  { path: '/', component: Login },
  { path: '/blog', component: Blog },
  { path: '/create-article', component: CreateArticle },
];

const router = createRouter({
  history: createWebHistory(process.env.BASE_URL),
  routes
});

export default router;
```

### 5. **主应用页面（`App.vue`）**

最后，你可以在 `App.vue` 中使用 `<router-view></router-view>` 来显示当前路由对应的页面。

```vue
<template>
  <div id="app">
    <router-view />
  </div>
</template>

<script>
export default {
  name: 'App',
};
</script>
```

---

### 小结

- **登录页面**：输入用户名和密码后，模拟跳转到博客主页。
- **博客主页**：展示简洁的文章列表，允许点击查看文章或者发布新文章。
- **发布文章页面**：允许输入标题和内容，点击发布后返回博客主页。

这样，你就可以实现一个简洁的博客前端页面。稍后你可以继续添加更多功能，如与后端交互、存储文章等。如果有任何问题，随时告诉我！

## 目录-功能

```
src/
├── assets/                   # 存放静态资源，如图片、样式等
│   └── logo.png
│   └── background.jpg
├── components/
│   ├── common/               # 通用组件
│   │   └── ArticleCard.vue   # 文章卡片组件，放在这里
│   │   ├── Button.vue        # 通用按钮
│   │   ├── Input.vue         # 通用输入框
│   │   └── Modal.vue         # 弹窗组件
ArticleCard.vue   # 文章卡片组件，放在这里
│   ├── auth/                 # 与身份验证相关的组件
│   │   ├── LoginPage.vue     # 登录页面
│   │   └── RegisterPage.vue  # 注册页面
│   ├── article/              # 与文章相关的组件
│   │   ├── Blog.vue          # 博客首页
│   │   ├── PublishArticle.vue  # 发布文章页面
│   │   ├── EditArticle.vue  # 编辑文章页面
│   ├── layout/               # 布局相关组件（例如头部、侧边栏等）
│   │   ├── Header.vue        # 页面头部
│   │   └── Sidebar.vue       # 侧边栏
│   ├── user/                 # 用户个人中心相关组件
│   │   ├── Profile.vue       # 用户个人资料
│   │   └── Settings.vue      # 用户设置页面
│   └── notification/         # 与通知相关的组件
│       ├── Notification.vue  # 通知组件
│       └── Alert.vue         # 警告提示组件
├── router/                   # 路由配置
│   └── index.js
├── store/                    # Vuex 状态管理
│   └── index.js
├── style/                    # 样式文件
│   └── main.css
└── ...

```

## 代码结构

### Commonent

#### common

#### auth

#### article

##### Blog.vue

```vue
```

##### ArticleCard.vue

```vue
<template>
  <div class="article-card">
    <h3>{{ article.title }}</h3>
    <p>{{ article.content }}</p>
    <router-link :to="`/article/${article.id}`">阅读更多</router-link>
  </div>
</template>

<script>
export default {
  name: 'ArticleCard',
  props: {
    article: {
      type: Object,
      required: true,
    },
  },
};
</script>

<style scoped>
.article-card {
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
  background-color: white;
}
</style>

```



