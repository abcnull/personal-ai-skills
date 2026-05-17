---
name: knl-be-springboot
description: 当用户需要快速捡起 springboot 后端开发知识，需要快速上手掌握 springboot 后端开发核心知识时使用
---

# springboot 后端开发知识概要

## 此文档目的

为了方便自己快速捡起 springboot 的后端开发的整体知识，此文档主要做一些整体的知识概要

## 简介

一般后端开发，老的 springboot 项目一般用：

- springboot 2 + jdk 8/11 + mybatis / mybatis-plus + mysql + maven

现在新的 springboot 项目一般用：

- springboot 3 + jdk 17+ + mybatis / mybatis-plus + mysql / postgresql + maven

springboot 2 和 springboot 3 的核心用法基本一致，区别主要在于底层依赖版本升级（如 spring 6、jdk 17 baseline），以及部分废弃的 API 被移除。

## 项目结构

```
project/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/demo/
│   │   │       ├── DemoApplication.java     # 启动类
│   │   │       ├── controller/              # 控制器层（接收请求）
│   │   │       ├── service/                 # 业务层
│   │   │       │   └── impl/
│   │   │       ├── mapper/                  # 数据访问层（mybatis）
│   │   │       ├── entity/                  # 实体类
│   │   │       ├── dto/                     # 数据传输对象
│   │   │       ├── vo/                      # 视图对象
│   │   │       ├── config/                  # 配置类
│   │   │       └── util/                    # 工具类
│   │   └── resources/
│   │       ├── application.yml              # 主配置文件
│   │       ├── application-dev.yml          # 开发环境配置
│   │       ├── application-prod.yml         # 生产环境配置
│   │       ├── mapper/                      # mybatis xml 映射文件（如用注解则无需）
│   │       └── static/                      # 静态资源
│   └── test/                                # 测试代码
│       └── java/
├── pom.xml                                  # maven 依赖配置
└── README.md
```

**springboot 2 与 springboot 3 主要区别：**

| 项 | springboot 2 | springboot 3 |
|---|---|---|
| 最低 JDK | JDK 8 | JDK 17 |
| Spring 版本 | Spring 5.x | Spring 6.x |
| Jakarta EE | `javax.*` 包名 | `jakarta.*` 包名 |
| 自动配置 | 基本相同 | 部分配置类路径调整 |

## 工作原理

### 整体执行流程

1. **启动类**：`DemoApplication.java`
   - 标注 `@SpringBootApplication`
   - 调用 `SpringApplication.run(DemoApplication.class, args)`

2. **加载配置**：`application.yml` / `application.properties`
   - 先加载配置文件，解析配置值
   - 按 `application-{profile}.yml` 加载多环境配置

3. **自动配置**：springboot 扫描 classpath，根据依赖自动装配 Bean
   - 基于加载的配置，创建并初始化 Bean（如 DataSource、SqlSessionFactory）
   - 如引入 `spring-boot-starter-web`，自动配置 Tomcat、Spring MVC
   - 如引入 `mybatis-spring-boot-starter`，自动配置数据源、SqlSessionFactory

4. **接收请求**：`Filter`（过滤器）→ `DispatcherServlet`（中央调度器）→ `Interceptor`（拦截器）
   - Filter 最先拦截（字符编码、跨域处理等）
   - DispatcherServlet 统一接收并分发请求
   - Interceptor 拦截处理（登录校验、权限检查等）

5. **路由分发**：`HandlerMapping` 根据 URL 找到对应的 Controller 方法

6. **业务处理**：`Controller` 层 → `Service` 层
   - Controller 接收参数，调用 Service
   - Service 处理业务逻辑

7. **数据访问**：`Mapper` 层（mybatis）或 `Repository` 层（jpa）
   - 操作数据库，返回结果

8. **响应返回**：结果沿原链路返回
   - Service → Controller 封装为 JSON
   - Interceptor → postHandle / afterCompletion
   - Filter → 响应输出 → HTTP Response

**Filter 与 Interceptor 的区别：**

| 项 | Filter | Interceptor |
|---|---|---|
| 所属 | Servlet 规范 | Spring MVC |
| 执行时机 | DispatcherServlet 之前 | DispatcherServlet 之后，Controller 之前 |
| 配置方式 | `@WebFilter` 或 `FilterRegistrationBean` | 实现 `HandlerInterceptor` + 注册 |
| 用途 | 字符编码、跨域、请求日志 | 登录校验、权限控制、性能统计 |

## 核心注解

### 启动类

| 注解 | 作用 |
|---|---|
| `@SpringBootApplication` | 组合注解（@Configuration + @EnableAutoConfiguration + @ComponentScan） |

### Controller 层

| 注解 | 作用 |
|---|---|
| `@RestController` | @Controller + @ResponseBody，返回 JSON |
| `@RequestMapping` | 映射请求 URL（可指定 method、path） |
| `@GetMapping` | 映射 GET 请求 |
| `@PostMapping` | 映射 POST 请求 |
| `@PutMapping` | 映射 PUT 请求 |
| `@DeleteMapping` | 映射 DELETE 请求 |
| `@RequestParam` | 接收 URL 参数（`?key=value`） |
| `@PathVariable` | 接收路径参数（`/user/{id}`） |
| `@RequestBody` | 接收 JSON 请求体 |

### Service 层

| 注解 | 作用 |
|---|---|
| `@Service` | 标注业务层组件 |
| `@Autowired` | 按类型自动注入依赖 |
| `@Resource` | 按名称自动注入依赖（JDK 标准） |
| `@Transactional` | 声明事务 |

### Mapper / 数据层

| 注解 | 作用 |
|---|---|
| `@Mapper` | mybatis mapper 接口标注 |
| `@MapperScan` | 扫描指定包下的所有 mapper |
| `@Select` / `@Insert` / `@Update` / `@Delete` | 直接写 SQL 注解（mybatis） |

### 配置类

| 注解 | 作用 |
|---|---|
| `@Configuration` | 标注配置类 |
| `@Bean` | 在配置类中定义 Bean |
| `@Value` | 读取配置文件值（`${key}`） |
| `@ConfigurationProperties` | 批量绑定配置前缀到对象 |

## 相关命令

| 命令 | 说明 |
|---|---|
| `mvn spring-boot:run` | 启动开发服务器 |
| `mvn clean package` | 清理并打包 |
| `mvn clean install` | 清理、编译、测试、打包、安装到本地仓库 |
| `mvn test` | 运行测试 |
| `java -jar target/demo-0.0.1-SNAPSHOT.jar` | 运行打包后的 jar |
| `./mvnw spring-boot:run` | 使用 maven wrapper 启动（无需本地安装 maven） |

Gradle 项目对应命令：

| 命令 | 说明 |
|---|---|
| `./gradlew bootRun` | 启动开发服务器 |
| `./gradlew build` | 构建项目 |
| `./gradlew test` | 运行测试 |
| `java -jar build/libs/demo-0.0.1-SNAPSHOT.jar` | 运行打包后的 jar |

## mybatis 常规基本使用方式

整体思路：**引入依赖 → 配置数据源 → 定义 Mapper 接口 → 编写 SQL → Service 调用**

```xml
<!-- pom.xml 依赖 -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>3.0.3</version>  <!-- sb3 用 3.x，sb2 用 2.x -->
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.33</version>
</dependency>
```

```yaml
# application.yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/demo?useUnicode=true&characterEncoding=utf-8
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.example.demo.entity
```

```java
// entity/User.java
public class User {
    private Long id;
    private String name;
    private Integer age;
    // getter / setter
}

// mapper/UserMapper.java
@Mapper
public interface UserMapper {
    @Select("SELECT * FROM user WHERE id = #{id}")
    User selectById(Long id);

    @Insert("INSERT INTO user(name, age) VALUES(#{name}, #{age})")
    int insert(User user);

    List<User> selectList();  // 复杂 SQL 走 xml
}
```

```xml
<!-- resources/mapper/UserMapper.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.demo.mapper.UserMapper">
    <select id="selectList" resultType="com.example.demo.entity.User">
        SELECT * FROM user
    </select>
</mapper>
```

```java
// service/UserService.java
@Service
public class UserService {
    @Autowired
    private UserMapper userMapper;

    public User getById(Long id) {
        return userMapper.selectById(id);
    }
}

// controller/UserController.java
@RestController
@RequestMapping("/users")
public class UserController {
    @Autowired
    private UserService userService;

    @GetMapping("/{id}")
    public User getById(@PathVariable Long id) {
        return userService.getById(id);
    }
}
```

## mybatis-plus 常规基本使用方式

mybatis-plus 是 mybatis 的增强工具，简化 CRUD 操作。

```xml
<!-- pom.xml -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.5.5</version>  <!-- sb3 用 3.5.5+，sb2 用 3.5.x -->
</dependency>
```

```java
// entity/User.java
@Data
@TableName("user")
public class User {
    @TableId(type = IdType.AUTO)   // 主键，自增
    private Long id;

    @TableField("user_name")        // 指定数据库字段名
    private String name;

    @TableField(fill = FieldFill.INSERT)  // 插入时自动填充
    private LocalDateTime createTime;

    @TableLogic                     // 逻辑删除标记
    private Integer deleted;

    @TableField(exist = false)      // 非数据库字段
    private String remark;
}

// mapper/UserMapper.java
@Mapper
public interface UserMapper extends BaseMapper<User> {
    // 继承 BaseMapper 后，基础的 CRUD 方法已经内置
}

// service/UserService.java
public interface UserService extends IService<User> {
}

@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {
}

// controller/UserController.java
@RestController
@RequestMapping("/users")
public class UserController {
    @Autowired
    private UserService userService;

    @GetMapping("/{id}")
    public User getById(@PathVariable Long id) {
        return userService.getById(id);
    }

    @GetMapping
    public List<User> list() {
        return userService.list();
    }

    @PostMapping
    public boolean save(@RequestBody User user) {
        return userService.save(user);
    }

    @DeleteMapping("/{id}")
    public boolean remove(@PathVariable Long id) {
        return userService.removeById(id);
    }
}
```

## redis 常规基本使用方式

整体思路：**引入依赖 → 配置连接 → 注入 RedisTemplate / StringRedisTemplate → 直接使用**

```xml
<!-- pom.xml -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

```yaml
# application.yml
spring:
  data:
    redis:
      host: localhost
      port: 6379
      password:       # 无密码留空
      database: 0
```

```java
// 直接使用
@Autowired
private StringRedisTemplate redisTemplate;

// 存
redisTemplate.opsForValue().set("key", "value", Duration.ofMinutes(10));
redisTemplate.opsForHash().put("user:1", "name", "张三");

// 取
String value = redisTemplate.opsForValue().get("key");
String name = (String) redisTemplate.opsForHash().get("user:1", "name");

// 删
redisTemplate.delete("key");
```

```java
// 封装工具类（推荐）
@Component
public class RedisUtil {
    @Autowired
    private StringRedisTemplate redisTemplate;

    public void set(String key, String value, long minutes) {
        redisTemplate.opsForValue().set(key, value, Duration.ofMinutes(minutes));
    }

    public String get(String key) {
        return redisTemplate.opsForValue().get(key);
    }

    public void del(String key) {
        redisTemplate.delete(key);
    }
}
```

## 日志配置常规基本使用方式

springboot 默认使用 **logback**，也可切换为 **log4j2**。

### logback（默认，无需额外依赖）

```xml
<!-- resources/logback-spring.xml -->
<configuration>
    <!-- 控制台输出 -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <!-- 文件输出 -->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>logs/app.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>logs/app.%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <!-- 根日志级别 -->
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="FILE"/>
    </root>

    <!-- 指定包日志级别 -->
    <logger name="com.example.demo.mapper" level="DEBUG"/>
</configuration>
```

### log4j2（需排除 logback 并引入 log4j2）

```xml
<!-- pom.xml -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

```xml
<!-- resources/log4j2-spring.xml -->
<Configuration>
    <Appenders>
        <Console name="CONSOLE" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
        <RollingFile name="FILE" fileName="logs/app.log"
                     filePattern="logs/app.%d{yyyy-MM-dd}.log">
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
            </Policies>
        </RollingFile>
    </Appenders>
    <Loggers>
        <Root level="INFO">
            <AppenderRef ref="CONSOLE"/>
            <AppenderRef ref="FILE"/>
        </Root>
    </Loggers>
</Configuration>
```

### 代码中使用（@Slf4j）

```java
import lombok.extern.slf4j.Slf4j;

@Slf4j
@Service
public class UserService {
    public void doSomething() {
        log.debug("调试信息");
        log.info("普通信息: {}", "参数");
        log.warn("警告信息");
        log.error("错误信息", exception);
    }
}
```

**日志级别**：`TRACE < DEBUG < INFO < WARN < ERROR`，配置级别越高，输出的日志越少。
