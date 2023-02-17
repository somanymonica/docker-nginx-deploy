# a.www.com/web1/

## 项目前提

已安装 nodejs。

## 项目创建

1. 创建基础项目
   ```
   # 目录/docker-nginx-deploy/a.xxx.com
   npx create-react-app web1
   ```
   修改部分代码
2. 新增`package.json`项目
   ```json
    "homepage": "site-a/web1/",
   ```
3. 打包静态文件
   ```
   # 目录/docker-nginx-deploy/a.xxx.com/web1
   npm run build
   ```
4. 在 basic-nginx 项目中上传文件并更新转发配置。
