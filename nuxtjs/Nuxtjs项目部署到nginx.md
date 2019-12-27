# Nuxtjs项目部署到nginx
Nuxt.js 提供了两种发布部署应用的方式：服务端渲染应用部署 和 静态应用部署。
注：我们公司部署用的是jenkins，以下所有的命令都可以在jenkins中配置，配置好一键部署就好了。

## 一、nuxtjs打包部署（静态应用部署）
```shell
yarn generate    # 这个命令会创建一个dist文件件，只要把dist丢到服务器就OK了。
```

## 二、服务器所需要的环境（服务器渲染）
流程：nuxtjs打包=>服务器部署运行=>nginx监听=>pm2进程守护

- nodejs [下载地址](http://nodejs.cn/download/).

- yarn 包管理工具
```shell
npm install -g yarn
```

## nuxtjs打包部署

```shell
yarn build       # 打包应用完成后，我们将：【.nuxt、nuxt.config.ts、package.json、static】传到服务器，然后在服务器上部署运行

yarn install     # 运行npm install 安装package里的依赖

yarn start       # 运行npm start 就可以运行起来nuxt的服务渲染了
```

## 配置nginx
代理监控3000端口，package打包时端口3000（默认）可在nuxt.config.ts或者package.json中修改

- 找到nginx配置文件（nginx.conf）
```shell
server {
  listen 80; 
  server_name www.ichaoge.com;
  location / {
    # 设置主机头和客户端真实地址，以便服务器获取客户端真实 IP
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    # 禁用缓存
    proxy_buffering off;
    # 反向代理的地址
    proxy_pass http://127.0.0.1:3001;
}
```

- 启动nginx
```shell
start nginx
```

小结：上述完事，我们就可以愉快的访问自己部署的应用了。

## pm2开启进程守护
pm2是一块非常优秀的node进程管理工具，它有丰富的特性：能够充分利用多核cpu且能够负载均衡、能够帮助应用在崩溃 后、指定时间和超出最大内存限制等 情况下实现自动重启。
pm2是开源的基于nodejs的进程管理器，包括守护进程，监控，日志的一整套完整的功能。

### pm2特点
- 内建负载均衡使用（node cluster）集群模块
- 后台运行
- 0秒停机重载
- 具有ubuntu和centOS的启动脚本
- 停止不稳定的进程（避免无线循环）
- 控制台检测



```shell
# 安装全局pm2
npm install pm2 -g

#服务器目录执行，SSR-service的名称是我们在package中的项目名称
pm2 start yarn --name "SSR-service" -- run start

# 完成后，我们用pm2 list查看进程列表
pm2 list

# 注：显示最有日志
pm2 logs

# 注：显示一个进程的日志
pm2 logs "SSR-service"
```

### pm2 常用命令
[官方文档](https://pm2.keymetrics.io/).
```shell
pm2 start app.js              # 启动app.js应用程序
pm2 start app.js -i 4         # cluster mode 模式启动4个app.js的应用实例
                              # 4个应用程序会自动进行负载均衡
pm2 start app.js --name="api" # 启动应用程序并命名为 "api"
pm2 start app.js --watch      # 当文件变化时自动重启应用
pm2 start script.sh           # 启动 bash 脚本
pm2 list                      # 列表 PM2 启动的所有的应用程序
pm2 monit                     # 显示每个应用程序的CPU和内存占用情况
pm2 show [app-name]           # 显示应用程序的所有信息
pm2 logs                      # 显示所有应用程序的日志
pm2 logs [app-name]           # 显示指定应用程序的日志
pm2 flush                     # 清空所有日志文件
pm2 stop all                  # 停止所有的应用程序
pm2 stop 0                    # 停止 id为 0的指定应用程序
pm2 restart all               # 重启所有应用
pm2 reload all                # 重启 cluster mode下的所有应用
pm2 gracefulReload all        # Graceful reload all apps in cluster mode
pm2 delete all                # 关闭并删除所有应用
pm2 delete 0                  # 删除指定应用 id 0
pm2 scale api 10              # 把名字叫api的应用扩展到10个实例
pm2 reset [app-name]          # 重置重启数量
pm2 startup                   # 创建开机自启动命令
pm2 save                      # 保存当前应用列表
pm2 resurrect                 # 重新加载保存的应用列表
pm2 update                    # Save processes, kill PM2 and restore processes
pm2 generate                  # Generate a sample json configuration file
```