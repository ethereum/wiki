---
name: Network Status
category: 
---

Special thanks to Kyle Chen for translation.

# 网络状态监控

以太坊（中心化的）网络状态监控器 （有时被称为“eth-netstats”），是一个基于网页的应用程序，通过一组节点去监控测试链或者主链的健康状态。

### 登记你的节点

要登记你的节点，你必须安装客户端的信息中继器，这是一个节点模块。这里给出的指示可以在Ubuntu Linux上使用（Mac OS X使用同样的指示，不过sudo指令可能不是必须的）。其他平台会有所变化（请确保nodejs-legacy也安装了，不然一些模块可能会失效。）

用Git clone指令复制程序库，然后用install指令安装PM2

    git clone https://github.com/cubedro/eth-net-intelligence-api
    cd eth-net-intelligence-api
    npm install
    sudo npm install -g pm2

然后编辑里面的app.json文件去配置你的节点：\

* 修改LISTENING_PORT选项右边的数值，将其改成以太坊的监听端口（默认：30303）
* 修改 INSTANCE_NAME选项右边的数值，改成你想给节点起的名字；
* 如果你想修改联系信息，将CONTACT_DETAILS选项右边的值改动
* 修改RPC_PORT选项右边的数值，改成你的节点的RPC端口（在cpp和go的版本中都是默认8545的端口）;
* 修改WS_SECRET选项右边的值，改成密令secret（你必须从官方的Skype联系渠道）获得

最后用以下的指令去运行进程：

    pm2 start app.json

一些指令是可用的：

* pm2 list 以显示进程状态;
* pm2 logs 以显示记录;
* pm2 gracefulReload node-app 以用于软重启;
* pm2 stop node-app 以停止应用程序;
* pm2 kill 以停止后台进程.

### 升级

如果想升级的话，需要根据以下步骤进行：

* git pull  以获取最新的版本
* sudo npm update 以更新程序依赖库
* pm2 gracefulReload node-app以重新载入客户端

## 在一个干净的Ubuntu系统里自动安装

获取和运行build shell。这会安装你需要的所有东西：在develop开发分支（你可以选eth或者geth）里面的ethereum – CLI, node.js, npm & pm2.

    bash <(curl https://raw.githubusercontent.com/cubedro/eth-net-intelligence-api/master/bin/build.sh)

### 配置

通过修改processes.json配置应用程序。注意你必须修改 ./bin/processes.json，这是processes.json的备份。（以让你可以设置环境变量，而不需要在更新的时候重写它）

    "env":
        {
            "NODE_ENV"        : "production", //告诉客户端我们在生产环境
            "RPC_HOST"        : "localhost", // eth JSON-RPC Host，默认是8545
            "RPC_PORT"        : "8545", // eth JSON-RPC 端口
            "LISTENING_PORT"  : "30303", // eth监听端口（只用于显示）
            "INSTANCE_NAME"   : "", // 你想给节点起的名字
            "CONTACT_DETAILS" : "", //如果你想的话可以在这里加入你的联系信息，如电子邮件或skype
            "WS_SERVER"       : "wss://stats.ethdev.com", //eth-netstats WebSockets api服务器的路径
            "WS_SECRET"       : "", // 用于登陆的WebSockets api 服务器密令secret    }

## 运行

使用pm2运行：

    cd ~/bin
    pm2 start processes.json

以太坊（eth或者geth）必须在允许rpc选项的情况下运行：

    geth --rpc

在geth下，默认的rpc端口（如果没有指定的话）是8545

## 升级

要升级API客户端的话就要使用如下的命令：

    ~/bin/www/bin/update.sh

这会停止当前的netstats客户端进程，自动检测你的以太坊的安装状态和版本，升级到最新的开发者版本，更新netstats客户端并重新载入进程。


