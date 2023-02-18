# b.www.com/project3/

## 项目前提

**特别声明**

> 1. 此项目是借用此处的[compose github 项目](https://github.com/docker/awesome-compose/tree/master/react-express-mysql)修改得来的项目。
>    > 此处 frontend 访问 backend 通过 Nginx 转发。
> 2. 如果不可以这样使用的话，麻烦大家告诉我～~

## 项目创建

1. 运行项目

   ```
   cd /docker-nginx-deploy/b.xxx.com/project3
   docker compose up -d
   ```

2. 新增`package.json`项目
   ```json
    "homepage": "project3/frontend/",
   ```
3. 打包 frontend 静态文件
   ```
   cd /docker-nginx-deploy/b.xxx.com/project3/frontend
   npm run build
   ```
4. 在 basic-nginx 项目中上传文件并更新转发配置。
