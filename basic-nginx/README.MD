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
