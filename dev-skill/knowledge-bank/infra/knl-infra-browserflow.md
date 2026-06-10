---
name: knl-infra-browserflow
description: 当用户想要了解从浏览器发送请求开始，请求如何经历 dns，nginx，api gateway，服务后端的，这篇文档可能可以提供帮助
---

# knl-infra-browserflow

这篇文章主要讲解了浏览器访问接口实际经过基础架构的流程，其中可能包括了 dns，nginx，agw，服务后端等基础架构

下面是这些基础架构的知识讲解，里头有些知识会比较琐碎

## 前置基础架构介绍

### 关于 DNS
dns 主要做的域名转发。

一般来说，有一定体量的公司通常都会部署自己的内网 DNS（域名解析系统），常见作解析内部域名。有体量的公司通常不会自己从零搭建公网 DNS 服务器，常见使用云 DNS 服务。

一般域名指定要跳转某个 ip，通过 dns 添加 A 记录或者 CNAME 记录来做到：
- A: 把域名直接指向一个 IP 地址（一个域名可以有多个 A 记录（做负载均衡））
- CNAME: 把域名指向另一个域名（别名）

一般来说
- 后端接口域名，有使用 A 或者 CNAME 的，如果固定 nginx ip 用 A，否则走 CNAME 配置
- 前端路由域名，一般使用 CNAME 配置 cdn 厂商指向的域名，即前端域名访问时，知道访问指定的 cdn

CNAME 和 A：
- 对于 CNAME 来说，本质就是把浏览器访问的 “别名” 映射到 “真实域名” 上，这个真实域名比如 cdn 厂商提供的域名，即 别名 => 真实域名 的映射，一般就是说加个规范域名记录
- 对于 A 来说，本质就是把浏览器访问的域名映射到 ip 上

### 关于 Nginx
nginx 一般用做反向代理，其实反向代理是基于正向代理说的，正向代理比如 charles，科学上网等
- 正向代理，相当于代替客户端发请求
- 反向代理，相当于代替服务器端接受请求

nginx 做反代，一般是接口域名请求过来，dns 告知浏览器要打到某个 nginx ip 上，nginx 中又配置了映射到哪个服务

一般来说，有体量的公司会用 agw 对服务的安全性等功能做处理，所以 nginx 中会配置接口域名映射到 agw 上，agw 中又会配置域名 + path 映射到哪个服务

国内大厂一般用 nginx 或者自研发。tengine 是 nginx 的一个 fork 版本，在 nginx 基础上做了加强，一般阿里系都在用，也兼容 nginx


nginx 具体配置:

【nginx -> 后端服务集群】
如果是 nginx 直接打到后端服务上：
```nginx
# =============================================
#  上游定义：后端服务集群（多个实例做负载均衡）
# =============================================
upstream order_service_pool {

    # 订单服务实例1
    server 10.2.1.1:8080 weight=5;

    # 订单服务实例2
    server 10.2.1.2:8080 weight=5;

    # 订单服务实例3
    server 10.2.1.3:8080 weight=3;

    # 保持长连接
    keepalive 32;
}

# =============================================
#  服务端配置：处理域名为 www.api.example.com 的请求
#  直接转发给后端服务集群
# =============================================
server {

    # 监听 443 端口，启用 HTTPS
    listen 443 ssl;

    # 匹配的域名
    server_name www.api.example.com;

    # ---------- SSL 证书配置 ----------
    ssl_certificate     /etc/nginx/ssl/www.api.example.com.pem;
    ssl_certificate_key /etc/nginx/ssl/www.api.example.com.key;
    ssl_protocols       TLSv1.2 TLSv1.3;

    # ---------- 日志配置 ----------
    access_log /var/log/nginx/www.api.example.com.access.log;
    error_log  /var/log/nginx/www.api.example.com.error.log;

    # ---------- 所有请求直接转发给后端服务 ----------
    location / {

        # 转发目标：后端服务集群（没有网关了，直接打到服务）
        proxy_pass http://order_service_pool;

        # ---------- 透传请求头（和场景一完全一样） ----------

        # 把原始域名传给后端服务
        proxy_set_header Host $host;

        # 把客户端真实 IP 传给后端服务
        proxy_set_header X-Real-IP $remote_addr;

        # 追加代理链路
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        # 告诉后端客户端最初用的是 http 还是 https
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

# =============================================
#  HTTP → HTTPS 强制跳转
# =============================================
server {
    listen 80;
    server_name www.api.example.com;
    return 301 https://$host$request_uri;
}
```
请求链路流程如下：
```
浏览器 GET https://www.api.example.com/api/order/12345
  │
  ▼
Nginx 收到请求
  Host: www.api.example.com
  Path: /api/order/12345
  │
  │  匹配 server_name www.api.example.com
  │  proxy_pass http://order_service_pool
  │  负载均衡选中 10.2.1.2:8080
  │
  ▼
order-service-2 直接收到请求并处理
  （认证、限流等需要在服务自身代码中实现）
```

【nginx -> agw】
如果是 nginx 打到 agw 上（一般企业这么做，通过 agw 做服务的熔断限流降级和安全性的功能，再通过 agw 打到后端服务）：
```nginx
# =============================================
#  上游定义：API 网关集群（多个网关实例做负载均衡）
# =============================================
upstream api_gateway_pool {

    # 网关实例1，权重5（接收更多流量）
    server 10.1.1.1:8080 weight=5;

    # 网关实例2，权重5
    server 10.1.1.2:8080 weight=5;

    # 网关实例3，权重3（性能稍弱，少分一些流量）
    server 10.1.1.3:8080 weight=3;

    # 保持长连接，减少频繁建立TCP连接的开销
    keepalive 32;
}

# =============================================
#  服务端配置：处理域名为 www.api.example.com 的请求
#  转发给 API 网关集群
# =============================================
server {

    # 监听 443 端口，启用 HTTPS
    listen 443 ssl;

    # 匹配的域名
    server_name www.api.example.com;

    # ---------- SSL 证书配置 ----------
    ssl_certificate     /etc/nginx/ssl/www.api.example.com.pem;
    ssl_certificate_key /etc/nginx/ssl/www.api.example.com.key;
    ssl_protocols       TLSv1.2 TLSv1.3;

    # ---------- 日志配置 ----------
    access_log /var/log/nginx/www.api.example.com.access.log;
    error_log  /var/log/nginx/www.api.example.com.error.log;

    # ---------- 所有请求转发给 API 网关 ----------
    location / {

        # 转发目标：API 网关集群
        proxy_pass http://api_gateway_pool;

        # ---------- 透传请求头（关键） ----------

        # 把原始域名传给网关，网关需要用它做 Host+Path 路由
        proxy_set_header Host $host;

        # 把客户端真实 IP 传给网关，网关的日志和限流需要它
        proxy_set_header X-Real-IP $remote_addr;

        # 追加代理链路，记录请求经过了哪些代理层
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        # 告诉网关客户端最初用的是 http 还是 https
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

# =============================================
#  HTTP → HTTPS 强制跳转
# =============================================
server {
    listen 80;
    server_name www.api.example.com;
    return 301 https://$host$request_uri;
}
```
请求链路流程如下：
```
浏览器 GET https://www.api.example.com/api/order/12345
  │
  ▼
Nginx 收到请求
  Host: www.api.example.com
  Path: /api/order/12345
  │
  │  匹配 server_name www.api.example.com
  │  proxy_pass http://api_gateway_pool
  │  负载均衡选中 10.1.1.2:8080
  │
  ▼
API 网关收到请求（从 Header 中拿到完整域名和路径）
  Host: www.api.example.com
  Path: /api/order/12345
  │
  │  匹配路由 [Host=www.api.example.com, Path=/api/order/**]
  │  执行认证、限流等
  │
  ▼
order-service 处理并返回
```

### 关于 AGW
agw 常用作路由、负载均衡、安全等功能，agw 非常常见的实现底层仍然是 nginx。包括如下能力：
- WAF 安全
- JWT/OAuth2 等认证
- 熔断限流
- 路由匹配
- 服务发现
- 等

一般每个后端服务启动时，会向注册中心注册自己的 ip:port，注册中心就能拿到服务 <=> ip:port 的映射关系。

agw 会实时拿到注册中心的节点列表数据，并存入 agw 的本地缓存中，agw 在负载均衡阶段会直接选择一个 ip 来发送

### 关于服务注册和服务发现

### 关于服务和集群

## 浏览器访问域名的整体流程
浏览器 -> dns -> (多层 cdn) -> nginx -> agw -> 后端服务

- nginx 在现在互联网中可以简单理解成专门用来做反代，和做到 agw 的负载均衡，还有 SSL
- agw 做业务服务的熔断限流安全，和业务服务的负载均衡，服务发现

像现在的阿里云腾讯云也都有类似 nginx，agw 的云产品可以提供接入

## 公司内给服务申请域名实际做了啥
公司新服务部署后，想要申请域名，可能需要填写相应表单申请，表单填写关键信息：
- 要申请的后端接口域名
- 对应的服务名

申请后，底层实际做了 2 个事情：
- 公司 nginx 或 agw 中做了后端接口域名 <=> 后端服务名的映射关系
- DNS 中 CNAME 记录后端接口域名要打到 nginx 的地址（实际可能更复杂比如多次 CNAME 映射）
