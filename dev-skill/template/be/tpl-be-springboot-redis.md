---
name: tpl-be-springboot-redis
description: 用户需要实现一个标准化的 springboot + redis 后端开发模版
disable-model-invocation: true
---

# springboot redis 开发模版

## 是否必须向用户确认
如果项目中该内容已经完成了一部分（如下所有需向用户确认的内容其实已经使用在项目中了），则延续用户所使用的技术栈能力继续标准化的开发即可，这种情况下无需向用户确认以下内容

如果项目中开发了部分的内容中，而且没有明确需要确认的如下技术栈能力，则必须要向用户确认：
- 使用 springboot 2.x 还是 springboot 3.x 版本（如果项目已经用了 springboot2 或者 springboot3 则无需向用户确认了）
- 使用的 JDK 版本（如果项目已经指定了 jdk 版本则无需向用户确认了）
- 使用 lettuce/jedis 还是哪种连接池（默认 lettuce）
- redis 连接信息（host、port、密码、数据库索引）

## 跟用户说明

向用户说明你整体的配置方式和步骤。向用户确认是否同意继续

## 开发：redis 配置

- 配置 redis 连接（application.yml）
- 注入 StringRedisTemplate 或 RedisTemplate
- 封装 RedisUtil 工具类（推荐）
- 代码中需要加上充分的注释，方便用户清晰知道每一步怎么配置

## 最后输出

- 告知用户 redis 配置的具体功能
- 告知用户关键配置想要修改在哪改，以及怎么改，怎么用
- 提醒用户 StringRedisTemplate 适合存字符串，RedisTemplate 适合存对象
