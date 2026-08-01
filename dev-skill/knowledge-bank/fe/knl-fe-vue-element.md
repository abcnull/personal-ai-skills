---
name: knl-fe-vue-element
description: 当用户需要快速捡起 vue 前端开发知识，需要快速上手掌握 vue 前端开发核心知识时使用
disable-model-invocation: true
---

# vue 前端开发知识概要

## 此文档目的

为了方便自己快速捡起 vue 的前端开发的整体知识，此文档主要做一些整体的知识概要

## 简介

一般企业做后台管理系统的前端 web 开发，老的 vue 项目一般用：

- vue 2 + element ui + vue cli(webpack 构建工具) + vue-router + axios
- 老的项目一般用 js

现在新的 vue 前端项目一般用：

- vue 3 + element plus + vite 构建工具 + vue-router + axios
- 新的项目推荐用 ts，但是 js 也很多在用，也完全兼容

## 项目结构

### vue cli（vue2）项目结构

```
project/
├── public/                  # 静态资源，不经过 webpack 处理
│   ├── index.html           # 入口 HTML 模板
│   └── favicon.ico
├── src/                     # 源码目录
│   ├── assets/              # 静态资源（图片、样式等）
│   ├── components/          # 公共组件
│   ├── views/               # 页面级组件
│   ├── router/              # 路由配置
│   │   └── index.js
│   ├── store/               # Vuex 状态管理
│   │   └── index.js
│   ├── App.vue              # 根组件
│   └── main.js              # 入口文件
├── .env                     # 环境变量（所有环境）
├── .env.development         # 开发环境变量
├── .env.production          # 生产环境变量
├── package.json             # 项目依赖和脚本配置
├── vue.config.js            # vue-cli 配置文件（可选）
└── babel.config.js          # babel 转译配置
```

### vite（vue3）项目结构

```
project/
├── public/                  # 静态资源，不经过 vite 处理
│   └── vite.svg
├── src/
│   ├── assets/              # 静态资源
│   ├── components/          # 公共组件
│   ├── views/               # 页面级组件
│   ├── router/              # 路由配置
│   │   └── index.js / index.ts
│   ├── stores/              # Pinia 状态管理（vue3 推荐）
│   │   └── counter.js
│   ├── App.vue
│   └── main.js / main.ts
├── index.html               # vite 入口 HTML（在根目录）
├── .env                     # 环境变量（所有环境）
├── .env.development         # 开发环境变量
├── .env.production          # 生产环境变量
├── package.json
├── vite.config.js / vite.config.ts
└── vite-env.d.ts            # vite 类型声明（ts 项目）
```

**主要区别：**

| 项 | vue2 (vue-cli) | vue3 (vite) |
|---|---|---|
| 构建工具 | webpack | vite |
| 入口 HTML | `public/index.html` | `index.html`（根目录） |
| 状态管理 | Vuex | Pinia（推荐）或 Vuex |
| 配置文件 | `vue.config.js` | `vite.config.js/ts` |
| 类型声明 | 无 | `vite-env.d.ts`（ts 项目） |

## 工作原理

### 整体执行流程

1. **入口文件**：`src/main.js`（或 `main.ts`）
   - vue2：`new Vue({ render: h => h(App) }).$mount('#app')`
   - vue3：`createApp(App).mount('#app')`

2. **挂载根组件**：`src/App.vue`
   - 根组件包含 `<router-view>` 等全局布局

3. **路由匹配**：`src/router/index.js`
   - 根据 URL 匹配对应页面组件

4. **页面渲染**：`src/views/xxx.vue`
   - 页面组件中使用 `components/` 下的公共组件

5. **数据请求**：通过 axios 调用后端 API
   - 一般在页面组件的 `created`（vue2）或 `onMounted`（vue3）中发起

6. **响应式更新**：数据变化后自动重新渲染视图
   - vue2：`Object.defineProperty` 劫持属性
   - vue3：`Proxy` 代理整个对象（性能更好，支持新增属性）

vue3 新增组合式 API（`<script setup>`），代码更紧凑，逻辑复用更方便。

## 相关命令

| 命令 | vue-cli（vue2） | vite（vue3） |
|---|---|---|
| 全局安装脚手架 | `npm install -g @vue/cli` | 无需全局安装 |
| 创建项目 | `vue create project-name` | `npm create vue@latest` |
| 安装依赖 | `npm install` | `npm install` |
| 启动开发服务器 | `npm run serve` | `npm run dev` |
| 生产构建 | `npm run build` | `npm run build` |
| 预览构建 | - | `npm run preview` |
| 代码检查 | `npm run lint` | `npm run lint` |
| 运行单元测试 | `npm run test:unit` | `npm run test:unit` |
| 运行 e2e 测试 | `npm run test:e2e` | `npm run test:e2e` |

## axios 常规基本使用方式

整体思路：**创建实例 → 配置拦截器 → 封装 API → 组件中调用**

```js
// 1. 创建实例（src/api/request.js）
import axios from 'axios'
const request = axios.create({
  baseURL: '/api',
  timeout: 10000
})

// 2. 拦截器：请求前加 token，响应后统一处理错误
request.interceptors.request.use(config => {
  const token = localStorage.getItem('token')
  if (token) config.headers.Authorization = `Bearer ${token}`
  return config
})
request.interceptors.response.use(
  res => res.data,
  err => { /* 统一处理 401 等错误 */ return Promise.reject(err) }
)

export default request

// 3. 封装 API（src/api/user.js）
import request from './request'
export const getUsers = (params) => request.get('/users', { params })
export const createUser = (data) => request.post('/users', data)

// 4. 组件中使用
import { getUsers } from '@/api/user'
const users = await getUsers({ page: 1 })
```

## vue-router 常规基本使用方式

整体思路：**定义路由表 → 创建 router 实例 → 注册到应用 → 组件中使用**

```js
// 1. 定义路由（src/router/index.js）
import { createRouter, createWebHistory } from 'vue-router'  // vue3
// import VueRouter from 'vue-router'                         // vue2

const routes = [
  { path: '/', component: () => import('@/views/Home.vue') },
  { path: '/user/:id', component: () => import('@/views/User.vue') },
  { path: '/:pathMatch(.*)*', component: () => import('@/views/404.vue') }  // vue3 通配符
]

const router = createRouter({ history: createWebHistory(), routes })  // vue3
// const router = new VueRouter({ mode: 'history', routes })          // vue2

// 路由守卫：登录校验
router.beforeEach((to, from, next) => {
  if (to.meta.requiresAuth && !localStorage.getItem('token')) {
    next('/login')
  } else {
    next()
  }
})

export default router

// 2. 注册到应用（main.js）
app.use(router)  // vue3
// new Vue({ router, render: h => h(App) }).$mount('#app')  // vue2
```

```vue
<!-- 3. 组件中使用 -->
<template>
  <router-link to="/">首页</router-link>
  <router-view></router-view>
</template>

<script setup>
import { useRoute, useRouter } from 'vue-router'  // vue3 组合式
const route = useRoute()   // 获取当前路由信息（params、query 等）
const router = useRouter() // 路由跳转方法
router.push('/user/123')

// vue2 中通过 this.$route / this.$router 访问
</script>
```
## element ui / element plus 常规基本使用方式

整体思路：**安装 → 全局引入 → 直接使用组件**

```bash
# vue2 对应 element-ui
npm install element-ui

# vue3 对应 element-plus
npm install element-plus
```

```js
// main.js 全局引入

// vue2 + element-ui
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
Vue.use(ElementUI)

// vue3 + element-plus
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
app.use(ElementPlus)
```

```vue
<!-- 组件中直接使用 -->
<template>
  <el-button type="primary" @click="handleClick">按钮</el-button>
  <el-table :data="tableData">
    <el-table-column prop="name" label="姓名" />
  </el-table>
  <el-dialog v-model="visible" title="提示">
    <span>内容</span>
  </el-dialog>
</template>
```

**主要区别：**

| 项 | element-ui（vue2） | element-plus（vue3） |
|---|---|---|
| 安装包 | `element-ui` | `element-plus` |
| 全局引入 | `Vue.use(ElementUI)` | `app.use(ElementPlus)` |
| CSS 路径 | `element-ui/lib/theme-chalk/index.css` | `element-plus/dist/index.css` |
| 组件用法 | 基本相同，部分属性名有调整（如 `v-model` 代替 `visible.sync`） |

### 配置文件介绍
#### package.json

**scripts 命令对比：**

| 命令 | vue-cli | vite |
|------|---------|------|
| 启动 | `npm run serve` | `npm run dev` |
| 构建 | `npm run build` | `npm run build` |
| lint | `npm run lint` | `npm run lint` |

**vue-cli 写法：**

```json
{
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  }
}
```

**vite 写法：**

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "lint": "eslint src"
  }
}
```

**依赖版本写法：**

| 写法 | 含义 |
|------|------|
| `^1.2.3` | 允许小版本更新（推荐） |
| `~1.2.3` | 仅允许补丁更新 |
| `1.2.3` | 精确版本 |

#### 环境配置文件

**命名规范：**

```
.env                # 所有环境加载
.env.development    # 开发环境
.env.production     # 生产环境
.env.local          # 本地覆盖（git 忽略）
```

**变量命名规范：**

| 项目类型 | 前缀要求 | 示例 |
|------|------|------|
| vue-cli | `VUE_APP_` | `VUE_APP_API_URL` |
| vite | `VITE_` | `VITE_API_URL` |

**使用方式：**

```js
// 代码中访问
process.env.VUE_APP_API_URL  // vue-cli
import.meta.env.VITE_API_URL // vite
```

#### vue.config.js / vite.config.js/ts

**常见配置项：**

```js
// vue.config.js（vue-cli）
module.exports = {
  devServer: {
    port: 8080,                    // 开发服务器端口
    proxy: { '/api': { target: 'http://localhost:3000' } }  // 代理
  },
  publicPath: '/',                 // 部署路径
  outputDir: 'dist',              // 输出目录
  lintOnSave: false               // 保存时是否 lint
}
```

```js
// vite.config.ts
import { defineConfig } from 'vite'
export default defineConfig({
  server: {
    port: 3000,                    // 开发服务器端口
    proxy: { '/api': { target: 'http://localhost:3000' } }  // 代理
  },
  base: '/',                      // 部署路径
  build: { outDir: 'dist' },      // 输出目录
  plugins: [vue()]                // 插件
})
```

**配置用途：**

| 配置 | 作用 |
|------|------|
| devServer/server | 开发服务器配置（端口、代理） |
| publicPath/base | 部署到非根目录时设置 |
| outputDir | 生产构建输出目录 |
| plugins | 集成插件（如 vue、jsx 等） |


