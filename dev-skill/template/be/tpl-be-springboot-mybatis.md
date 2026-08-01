---
name: tpl-be-springboot-mybatis
description: 用户需要实现一个标准化的 springboot + mybatis 后端开发模版
disable-model-invocation: true
---

# springboot mybatis 开发模版

## 是否必须向用户确认
如果项目中该内容已经完成了一部分（如下所有需向用户确认的内容其实已经使用在项目中了），则延续用户所使用的技术栈能力继续标准化的开发即可，这种情况下无需向用户确认以下内容

如果项目中开发了部分的内容中，而且没有明确需要确认的如下技术栈能力，则必须要向用户确认：
- 使用 springboot 2.x 还是 springboot 3.x 版本（如果项目已经用了 springboot2 或者 springboot3 则无需向用户确认了）
- 使用的 JDK 版本（如果项目已经指定了 jdk 版本则无需向用户确认了）
- mysql 连接配置信息（host、port、数据库名、用户名、密码）

## 跟用户说明

向用户说明你整体的配置方式和步骤。向用户确认是否同意继续

## 开发：mybatis 配置

- 配置数据源（application.yml）
- 配置 mybatis（mapper-locations、type-aliases-package）
- 创建 Mapper 接口（使用 @Mapper 或 @MapperScan）
- 编写 xml 映射文件（resources/mapper/ 目录下）
- xml 文件中需要加上充分的注释，方便用户清晰知道每一步怎么配置

## 最后输出

- 告知用户 mybatis 配置的具体功能
- 告知用户关键配置想要修改在哪改，以及怎么改，怎么用
