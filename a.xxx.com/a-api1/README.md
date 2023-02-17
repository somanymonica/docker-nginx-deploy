# a.www.com/api1/

## 项目前提

1. 已安装 python3
2. 会使用 python 虚拟环境工具 virtualenv

## 项目创建

1. 安装环境
   ```
   mkvirtualenv a-web1
   pip install django
   ```
2. 创建基础项目

   ```
   # 创建项目
   cd /docker-nginx-deploy/a.xxx.com
   django-admin startproject backend

   # 重命名文件夹
   mv backend a-api1

    # 生成依赖
   cd a-api1
   pip freeze >requirements.txt

   # 退出虚拟环境
   deactivate
   ```

3. 更新项目配置
   - 在`a-api1/backend/settings.py`更新项目
     `ALLOWED_HOSTS = ['*']`
   - 新增 docker 文件
4. 运行项目
   ```
   cd /docker-nginx-deploy/a.xxx.com/a-api1
   docker compose up -d
   ```
   测试项目时可以先在 compose.yml 中绑定本地端口，在浏览器中查看运行情况。
5. 在 basic-nginx 中配置转发路径。
