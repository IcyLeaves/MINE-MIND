# Node.js

*summary*

<img src="./NodeJs.assets/1.jpg" alt="img" style="zoom:50%;" />

**NodeJs**是一个轻量级Js运行环境。

---

*pinned*

### Linux安装NodeJs环境

> [在Linux系统安装Nodejs 最简单步骤 - liuqiceshi - 博客园 (cnblogs.com)](https://www.cnblogs.com/liuqi/p/6483317.html)
>
> [下载 | Node.js 中文网 (nodejs.cn)](http://nodejs.cn/download/)

- 下载文件：`wget https://npm.taobao.org/mirrors/node/v16.5.0/node-v16.5.0-linux-x64.tar.xz`
- 解压：`tar -xvf node-v16.5.0-linux-x64.tar.xz`
- 重命名：`mv node-v16.5.0-linux-x64 nodejs`
- cd到nodejs/bin目录下获取完整路径：`pwd`
- 建立软链接：`ln -s /{your_path}/gartic-phone/nodejs/bin /usr/local/bin/`

---

*2021.07.31*

### 搭建一个网站的镜像站

> [100行代码完成github镜像功能_weixin_34342578的博客-CSDN博客](https://blog.csdn.net/weixin_34342578/article/details/91693910)
>
> [Express "Hello World" example - Express 中文文档 | Express 中文网 (expressjs.com.cn)](https://www.expressjs.com.cn/starter/hello-world.html)

最近你画我猜的网页版Gartic Phone很火，但是朋友们都苦于服务器频繁的重连。因此我想搭建一个镜像站来转发请求。 

#### 准备好镜像站的域名

- 本地测试的网址可以是：`http://localhost:12345`
- 公共网址可以是：`http://my.gartic.phone`（当然这里要转发到镜像站监听的端口上去）

#### 核心代码

- 核心代码index.js见上面的Github链接，这里提几个重要的点
- 项目的运行环境
  - 依赖：`express`和`request`
  - 运行：`node index.js`
  - Linux后台运行：`nohup node index.js > myLog.log 2>&1 &`
- 项目配置
  - 接口：`listenPort`的值
  - 目标网站：`tgt`的值

