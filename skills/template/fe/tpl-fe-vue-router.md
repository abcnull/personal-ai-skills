---
name: tpl-fe-vue-router
description: 用户需要实现一个标准化的 vue 前端路由配置模版
---

# vue-router 路由配置模版

## 是否必须向用户确认
如果项目中该内容已经完成了一部分（如下所有需向用户确认的内容其实已经使用在项目中了），则延续用户所使用的技术栈能力继续标准化的开发即可，这种情况下无需向用户确认以下内容

如果项目中开发了部分的内容中，而且没有明确需要确认的如下技术栈能力，则必须要向用户确认：
- 使用 vue 2 还是 vue 3（如果项目已经用了 vue2 或者 vue3 则无需向用户确认了）
- 使用 JavaScript 还是 TypeScript（如果项目已经用了 JavaScript 或者 TypeScript 则无需向用户确认了）
- 向用户确认所有路由路径

## 跟用户说明

向用户说明你整体的配置方式和步骤。向用户确认是否同意继续

## vue-router 配置

- 安装 vue-router（如果没装的话）
- 路由模式使用 hash 模式
- 创建 router/index.js 路由配置文件
- 定义路由表（含路由懒加载）
- 配置路由守卫（beforeEach 等）
- 注册到应用（main.js）
- 代码中需要加上充分的注释，方便用户清晰知道每一步怎么配置

## 最后输出

- 告知用户 vue-router 配置的具体功能
- 告知用户关键配置想要修改在哪改，以及怎么改，怎么用
- 提醒用户 vue3 使用 createRouter/createWebHistory，vue2 使用 new VueRouter
