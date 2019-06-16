## 无公网IP下链接远程服务器
1. teamviewer  （安装使用，不多做解释）
2. ngrok        （通过代理链接）

### ngrok 安装说明
1. [下载ngrok](https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip)
2. unzip /path/to/ngrok.zip
3. ./ngrok authtoken qLDHjs7s87JMcGwRx8Zw_43jTtgBrSNjTx1MXRFbsw
4. ./ngrok tcp 22


### 参考文档
1. [如何远程登录家里的Ubuntu电脑(命令行模式)](https://www.zhihu.com/question/27771692)
2. [ngrok Documentation](https://ngrok.com/docs)
3. [Setup & Installation](https://dashboard.ngrok.com/get-started)