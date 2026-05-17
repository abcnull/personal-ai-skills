---
name: tpl-be-springboot-mybatisplus
description: 用户需要实现一个标准化的 springboot + mybatis-plus 后端开发模版
---

# springboot mybatis-plus 开发模版

## 必须向用户确认

- 使用 springboot 2.x 还是 springboot 3.x 版本（如果项目已经用了 springboot2 或者 springboot3 则无需向用户确认了）
- 使用的 JDK 版本（如果项目已经指定了 jdk 版本则无需向用户确认了）
- mysql 连接配置信息（host、port、数据库名、用户名、密码）

## 跟用户说明

向用户说明你整体的配置方式和步骤。向用户确认是否同意继续

## mybatis-plus 配置

- 配置数据源（application.yml）
- 创建实体类（使用 @TableName、@TableId、@TableField 等注解）
- 创建 Mapper 接口（继承 BaseMapper<T>）
- 创建 Service 接口（继承 IService<T>）和实现类（继承 ServiceImpl）
- 代码中需要加上充分的注释，方便用户清晰知道每一步怎么配置

## 最后输出

- 告知用户 mybatis-plus 配置的具体功能
- 告知用户关键配置想要修改在哪改，以及怎么改，怎么用
- 提醒用户 BaseMapper 和 IService 已经内置了常用的 CRUD 方法
