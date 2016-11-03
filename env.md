# 环境配置

客户端版本需要配合服务端使用，因此，前期我们需要进行环境配置，将服务端启动。（这边选择koa版本的服务端）

### jackblog-api-koa部署配置

* 安装 [Visual C++ Build Tools](http://landinghub.visualstudio.com/visual-cpp-build-tools)（默认安装到C盘）

* 安装 [Python 2.7](https://www.python.org/downloads/)（必须是2.7版本，别安装3.0以上的版本）

* CMD中设置：`npm config set msvs_version 2015`

* 把npm更新到最新版本：`npm -g install npm@next`

* 安装 [Redis数据库](https://github.com/MSOpenTech/redis/releases)

* CD到 jackblog-api-koa 目录 `npm install` 

* 启动 mongo 和 redis 数据库 

* 启动服务 `npm start`


注意：

1. 以上配置适用于 Windows 10 环境

2. 建议配置 redis 环境变量，并以守护进程方式启动 redis：进入 `redis.windows.conf` 文件，修改 daemonize （该参数用语确定redis进程以什么方式启动）为yes 。`redis-server` 启动服务器。

3. 启动服务端时，有可能 ccap 模块会报错，无法正常启动。可以将这个模块注释，不影响我们看客户端的版本。

### jackblog-vue部署配置

客户端这边，基本上只要用 `npm install` 将依赖包安装上就可以了。

注意一个点，不同的操作系统中可能会出现不同的编辑器，行结束方式也不同，因此我们需要修改根目录下的 `.eslintrc.json` 文件里面的 linebreak-style属性，修改为 `"linebreak-style":0` 。










