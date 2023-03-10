# 基础 Nginx 代理应用

## 应用概述

1. 使用该 Nginx 服务管理与转发所有其他应用。
2. 当修改了配置时，应及时重启服务。
   ```
   # 在 /docker-nginx-deploy/basic-nginx 目录下操作
   docker compose restart
   ```

## 构建步骤

### 1. 基础

1. 创建基础文件结构
   ```
   .
    ├── compose.yml
    ├── conf.d
    │   └── default.conf
    ├── dist
    │   ├── 50x.html
    │   └── index.html
    └── log
        ├── access.log
        └── error.log
   ```
2. `compose.yml`映射对应文件夹与文件，绑定主机 80 端口

   - `default.conf`/`50x.html`/`index.html`为 nginx 默认文件

   ```yml
   version: "3"

    services:
    basic-nginx:
        image: nginx:1.21.6
        restart: always
        volumes:
        - ./conf.d:/etc/nginx/conf.d
        - ./log:/var/log/nginx
        - ./dist:/usr/share/nginx/html
        ports:
        - "80:80"

   ```

3. 在目录`/docker-nginx-deploy/basic-nginx`下开启服务
   ```
   docker compose up -d
   ```
4. 成功预期：浏览器在`http://localhost`可以看到 Nginx 服务已开启。

### 2. a.xxx.com 项目

1. 创建静态文件目录
   ```
   cd /docker-nginx-deploy/basic-nginx/dist
   mkdir site-a
   ```
2. 创建 nginx 配置文件
   ```
   cd /docker-nginx-deploy/basic-nginx/conf.d
   touch a.xxx.com.conf
   ```
3. 基础配置文件`/docker-nginx-deploy/basic-nginx/conf.d/a.xxx.com.conf`

   ```
   server {
        listen       80;
        server_name  a.xxx.com;

        location / {
            root   /usr/share/nginx/html;
            index  index.html;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }
   ```

#### 2.1 前端项目 web1

1. 创建 web1 项目并打包
2. 将静态文件复制到 nginx 的目录`/usr/share/nginx/html`，也就是`/docker-nginx-deploy/basic-nginx/dist`
   ```
   cd site-a
   cp -r /docker-nginx-deploy/a.xxx.com/web1/build web1
   ```
3. 在配置文件`a.xxx.com.conf`中新增 location 转发
   ```
   location /web1 {
        alias   /usr/share/nginx/html/;
        index  site-a/web1/index.html;
    }
   ```
4. 重启 basic-nginx 服务
   ```
   cd /docker-nginx-deploy/basic-nginx
   docker compose restart
   ```
5. 预期结果：浏览器打开`http://a.xxx.com/web1/`能看到页面。

#### 2.2 前端项目 web2

1. 创建 web2 项目并打包
2. 将静态文件复制到 nginx 的目录`/usr/share/nginx/html`，也就是`/docker-nginx-deploy/basic-nginx/dist`
   ```
   cd site-a
   cp -r /docker-nginx-deploy/a.xxx.com/web2/build web2
   ```
3. 在配置文件`a.xxx.com.conf`中新增 location 转发
   ```
   location /web2 {
        alias   /usr/share/nginx/html/;
        index  site-a/web2/index.html;
    }
   ```
4. 重启 basic-nginx 服务
   ```
   cd /docker-nginx-deploy/basic-nginx
   docker compose restart
   ```
5. 预期结果：浏览器打开`http://a.xxx.com/web2/`能看到页面。

#### 2.3 Django 后端项目 api1

1. 创建 a-api1 项目，并运行该项目容器
2. 新增`/docker-nginx-deploy/basic-nginx/compose.yml`中的外部网络
   - 便于在配置中使用 hostname
3. 在配置文件`a.xxx.com.conf`中新增 location 转发
   ```
   # 1. 注意后端转发路径末尾/
   # 2. 通过docker network可以使用在/docker-nginx-deploy/a.xxx.com/a-api1/compose.yml 中定义的服务名代替IP
   location /api1 {
        proxy_pass http://sitea-api1:8000/;
    }
   ```
4. 重启 basic-nginx 服务
   ```
   cd /docker-nginx-deploy/basic-nginx
   docker compose restart
   ```
5. 预期结果：浏览器打开`http://a.xxx.com/api1/`能看到页面。

#### 2.4 Flask 后端项目 api2

1. 创建 a-api2 项目，并运行该项目容器
2. 新增`/docker-nginx-deploy/basic-nginx/compose.yml`中的外部网络
   - 便于在配置中使用 hostname
3. 在配置文件`a.xxx.com.conf`中新增 location 转发
   ```
   # 1. 注意后端转发路径末尾/
   # 2. 通过docker network可以使用在/docker-nginx-deploy/a.xxx.com/a-api2/compose.yml 中定义的服务名代替IP
   location /api2 {
        proxy_pass http://sitea-api2:8000/;
    }
   ```
4. 重启 basic-nginx 服务
   ```
   cd /docker-nginx-deploy/basic-nginx
   docker compose restart
   ```
5. 预期结果：浏览器打开`http://a.xxx.com/api2/`能看到页面。

### 3. b.xxx.com 项目

1. 创建项目目录
   ```
   cd /docker-nginx-deploy
   mkdir b.xxx.com
   ```
2. 创建 nginx 配置文件
   ```
   cd /docker-nginx-deploy/basic-nginx/conf.d
   touch b.xxx.com.conf
   ```
3. 基础配置文件`/docker-nginx-deploy/basic-nginx/conf.d/b.xxx.com.conf`

   ```
   server {
      listen       80;
      server_name  b.xxx.com;

      location / {
         root   /usr/share/nginx/html;
         index  index.html;
      }

      error_page   500 502 503 504  /50x.html;
      location = /50x.html {
         root   /usr/share/nginx/html;
      }
   }
   ```

#### 3.1 后端项目 Project2

1. 创建 project2 项目，并运行该项目容器
2. 在配置文件`b.xxx.com.conf`中新增 location 转发
   ```
   # 1. 注意后端转发路径末尾/
   location /project2 {
        proxy_pass http://siteb-p2-backend:8000/;
    }
   ```
3. 重启 basic-nginx 服务
   ```
   cd /docker-nginx-deploy/basic-nginx
   docker compose restart
   ```
4. 预期结果：浏览器打开`http://b.xxx.com/project2/`能看到页面。

#### 3.2 全栈项目 Project3

1. 前端项目

   1. 创建静态文件目录

   ```
   cd /docker-nginx-deploy/basic-nginx/dist
   mkdir -p site-b/project3
   ```

   2. 将静态文件复制到 nginx 的目录

   ```
   cd site-b/project3
   cp -r /docker-nginx-deploy/b.xxx.com/project3/frontend/build frontend
   ```

   4. Nginx 转发前端项目

   ```
   location /project3 {
        alias   /usr/share/nginx/html/site-b/project3;
        index  frontend/index.html;
    }
   ```

2. nginx 转发后端服务
   1. 运行 project3 服务
      ```
      cd /docker-nginx-deploy/b.xxx.com/project3
      docker compose up -d
      ```
   2. 在配置文件`b.xxx.com.conf`中新增 project3 后端服务 location 转发
      ```
      # 1. 注意后端转发路径末尾/
      location /api/ {
         proxy_pass http://siteb-p3-backend:8001/;
      }
      ```
3. 重启 basic-nginx 服务
   ```
   cd /docker-nginx-deploy/basic-nginx
   docker compose restart
   ```
4. 预期结果：浏览器打开`http://b.xxx.com/project3/`能看到页面。
