# a.www.com/web2/

## 项目前提

已安装 nodejs。

## 项目创建

1. 创建基础项目
   ```
   cd /docker-nginx-deploy/a.xxx.com
   npx create-react-app web2
   ```
   修改部分代码
2. 新增`package.json`项目
   ```json
    "homepage": "site-a/web2/",
   ```
3. 打包静态文件
   ```
   cd /docker-nginx-deploy/a.xxx.com/web2
   npm run build
   ```
4. 在 basic-nginx 项目中上传文件并更新转发配置。
