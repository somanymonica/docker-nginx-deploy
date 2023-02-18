# docker-nginx-deploy

> A deploy example that can be used to deploy your multi-services in your server by using docker and nginx.

## 应用背景

1. 目的是在服务器使用 nginx 管理多个服务。
2. 因为其他服务都是用 docker 容器管理运行，为方便 nginx 服务使用 docker network 访问其他服务，所以也使用 docker 来管理 Nginx 代理。
3. 服务器只开放 80 端口，所有服务使用 Nginx 代理转发。
4. 有子域名两个，分别是`a.xxx.com`，`b.xxx.com`。
5. 计划有三大项目：
   1. Project1: Reactjs(前端 1) + Reactjs(前端 2) + Django(后端 1) + Flask(后端 2)
   2. Project2(后端全栈服务): Flask + Mysql
   3. Project3(前端全栈服务): Reactjs + Nodejs + Mysql
6. 项目与各域名的部署情况
   1. a.xxx.com：
      - web1/: Reactjs(前端 1)
      - web2/: Reactjs(前端 2)
      - api1/: Django(后端 1)
      - api2/: Flask(后端 2)
   2. b.xxx.com:
      - project2/: Flask
      - project3/: Reactjs

## 文件结构

```
.
├── basic-nginx/
├── a.xxx.com/
└── b.xxx.com/
```

## 项目前提

1. 熟悉 docker 及 docker compose 基础
2. 在 /etc/hosts 中新增两个域名用于测试
   ```
   127.0.0.1 a.xxx.com
   127.0.0.1 b.xxx.com
   ```
3. 创建一个公共的网络用于 Nginx 代理与其他服务沟通
   ```
   docker network create proxy-network
   ```

## 网络关系

```plantuml
@startuml
title **Network**
actor "User/blowser" as User #red

box "Proxy" #LightBlue
control "Nginx" as A
end box
box "Servers"
collections "Static files" as B
entity "Backends" as C
database "Databases" as D
end box

== a.xxx.com/web1 ==
User -> A: a.xxx.com/web1
A -\ B: /usr/share/nginx/html/site-a/web1/
B -\ A: [static files]
A -> User: [static files]

== a.xxx.com/web2 ==
User -> A: a.xxx.com/web2
A -\ B: /usr/share/nginx/html/site-a/web2/
B -\ A: [static files]
A -> User: [static files]

== a.xxx.com/api1 ==
User -> A: a.xxx.com
A o->o C: /api1 => http://sitea-api1:8000/
activate C
C o->o A: Response
deactivate C
A -> User: Response

== a.xxx.com/api2 ==
User -> A: a.xxx.com
A o->o C: /api2 => http://sitea-api2:8000/
activate C
C o->o A: Response
deactivate C
A -> User: Response

== b.xxx.com/project2 ==
User -> A: b.xxx.com
A o->o C: /project2 => http://siteb-p2-backend:8000/
activate C
C --> D: queries
activate D
D --> C: data results
deactivate D
C o->o A: Response
deactivate C
A -> User: Response

== b.xxx.com/project3 ==
User -> A: b.xxx.com/project3
A -\ B: /usr/share/nginx/html/site-b/project3
activate B
B -\o A: fetch("/api/")
A o->o C: /api/ => http://siteb-p3-backend:8001/
activate C
C --> D: queries
activate D
D --> C: data results
deactivate D
C o->o A: Response
deactivate C
A -\ B: Response
B -\ A: [updated static files]
deactivate B
A -> User: [static files]



@enduml
```
