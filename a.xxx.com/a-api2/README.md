# a.www.com/api2/

## 项目前提

1. 已安装 python3
2. 会使用 python 虚拟环境工具 virtualenv

## 项目创建

1. 安装环境
   ```
   mkvirtualenv a-api2
   pip install flask
   ```
2. 创建基础项目

   ```
   # 创建项目
   cd /docker-nginx-deploy/a.xxx.com
   mkdir a-api2
   cd a-api2

    # 生成依赖
   cd a-api2
   pip freeze >requirements.txt

   # 退出虚拟环境
   deactivate

   touch app.py
   ```

3. 创建 docker 文件
4. 运行项目
   ```
   cd /docker-nginx-deploy/a.xxx.com/a-api2
   docker compose up -d
   ```
   测试项目时可以先在 compose.yml 中绑定本地端口，在浏览器中查看运行情况。
5. 在 basic-nginx 中配置转发路径。
