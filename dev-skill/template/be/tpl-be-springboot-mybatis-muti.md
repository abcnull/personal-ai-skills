---
name: tpl-be-springboot-mybatis-muti
description: 用户需要实现一个标准化的 springboot + mybatis 多数据源后端开发模版
---

# springboot mybatis 多数据源开发模版

## 是否必须向用户确认
如果项目中该内容已经完成了一部分（如下所有需向用户确认的内容其实已经使用在项目中了），则延续用户所使用的技术栈能力继续标准化的开发即可，这种情况下无需向用户确认以下内容

如果项目中开发了部分的内容中，而且没有明确需要确认的如下技术栈能力，则必须要向用户确认：
- 使用 springboot 2.x 还是 springboot 3.x 版本（如果项目已经用了 springboot2 或者 springboot3 则无需向用户确认了）
- 使用的 JDK 版本（如果项目已经指定了 jdk 版本则无需向用户确认了）
- 需要连接几个数据源，各自的 mysql 连接配置信息
- mapper 接口和对应 xml 想要存放的路径是啥（如果用户没有告知则标准化的存放）

## 跟用户说明

向用户说明你整体的配置方式和步骤，包括：
- 多数据源需要手动配置 DataSource、SqlSessionFactory、TransactionManager
- 使用 @Primary 标注主数据源
- 使用 @MapperScan 分别扫描不同数据源的 Mapper
- 向用户确认是否同意继续

## 开发：多数据源配置

- 配置多个数据源（application.yml 中定义多个 datasource）
- 创建 DataSourceConfig 配置类，分别创建多个 DataSource Bean
- 为每个数据源配置 SqlSessionFactory 和 TransactionManager
- 使用 @MapperScan 指定每个 Mapper 接口对应的数据源
- 代码中需要加上充分的注释，方便用户清晰知道每一步怎么配置

## 最后输出

- 告知用户多数据源配置的具体功能和怎么用
- 告知用户关键配置想要修改在哪改，以及怎么改
- 提醒用户事务管理需要指定对应的数据源 TransactionManager
