# Mattermost

*summary*

<img src="./Mattermost.assets/图片123.png" alt="图片123" style="zoom:80%;" />

**Mattermost**是一个聊天工具

---

*pinned*

### 安装Docker版

- 非官方的安装教程（不过很管用）：[Deploy Mattermost on Docker](https://docs.mattermost.com/install/prod-docker.html)

#### 改动：不安装web镜像

- 因为我们可以用自己的nginx进行反向代理。

- 在`{mattermost_docker}/docker-compose.yml`中注释web部分，并在app处开放端口

  ```yaml
  app:
    ports:
      - "14080:8000"
      - "14443:443"
      
  # web:
  #   build: web
  #   ports:
  #     - "80:80"
  #     - "443:443"
  #   read_only: true
  #   restart: unless-stopped
  #   volumes:
  #     # This directory must have cert files if you want to enable SSL
  #     - ./volumes/web/cert:/cert:ro
  #     - /etc/localtime:/etc/localtime:ro
  ```

#### 改动：国内镜像源添加

- 在`{mattermost_docker}/app/Dockerfile`中添加apk仓库源

  ```dockerfile
  # ICY's Changes
  RUN echo -e http://mirrors.ustc.edu.cn/alpine/v3.11/main/ > /etc/apk/repositories
  RUN echo -e http://mirrors.ustc.edu.cn/alpine/v3.11/community/ >> /etc/apk/repositories
  # Install some needed packages
  # ...
  ```

- 在`{mattermost_docker}/db/Dockerfile`中添加apk仓库源，pip镜像源

  ```Dockerfile
  # ICY's Changes
  RUN echo -e http://mirrors.ustc.edu.cn/alpine/v3.11/main/ > /etc/apk/repositories
  RUN echo -e http://mirrors.ustc.edu.cn/alpine/v3.11/community/ >> /etc/apk/repositories
  # ICY's Changes pip
  RUN echo "azure<5.0.0" > pip-constraints.txt
  RUN apk add --no-cache \
        build-base \
        curl \
        libc6-compat \
        libffi-dev \
        linux-headers \
        python-dev \
        py-pip \
        py-cryptography \
        pv \
        libressl-dev \
      && pip install -i https://mirrors.aliyun.com/pypi/simple/ --upgrade pip \
      && pip --no-cache-dir install -i https://mirrors.aliyun.com/pypi/simple/ -c pip-constraints.txt 'wal-e<1.0.0' envdir \
      && rm -rf /var/cache/apk/* /tmp/* /var/tmp/*
  ```

---

*2021.01.05*

### 多层nginx+挂载文件的配置修改之路

> 这个问题并未完全解决，以下草稿仅供参考，很可能有杂乱/缺失/错误等情况

web是nginx

dockerfile copy文件

entrypoint 软连接

还得手动修改/etc/nginx/conf.d然后nginx -s reload

下次停用web，因为外部已经有nginx代理  或者 研究透彻它的原理

---

*2021.04.13*

### Gauge测试UI+Jenkins流水线+Mattermost消息订阅（Mattermost篇）

其他两篇相关文章为

- [Gauge测试UI+Jenkins流水线+Mattermost消息订阅（Gauge篇）](../前端%20FrontEnd/Gauge.md/#gauge测试uijenkins流水线mattermost消息订阅gauge篇)
- [Gauge测试UI+Jenkins流水线+Mattermost消息订阅（Jenkins篇）](../运维%20DevOps/Jenkins.md/#gauge测试uijenkins流水线mattermost消息订阅jenkins篇)

> [持续交付流水线中的消息传递与协作实现](https://blog.csdn.net/weixin_40046357/article/details/106464610)

#### Jenkins安装Mattermost订阅插件

- 在上篇Jenkins Job获取到测试情况后，我们可以根据结果进行不同的处理

  - 如果测试通过，则只是保存测试报告而不告警。30min后重新启动测试
  - 如果测试失败，需要将“测试失败”消息和测试报告URL推送至Mattermost的频道。不会再次启动测试，需要在Mattermost使用斜杠命令重启job

- Jenkins安装Mattermost插件，之后便可在pipeline中发送消息：

  ![image-20210415110256778](Mattermost.assets/image-20210415110256778.png)


#### Mattermost添加集成

- 首先创建一个接收消息的频道**jenkins_hook**

- **团队选项 > 集成 > 传入Webhooks** 中添加新的引入勾子：

  - 用户名：jenkins
  - 个人资料照片：`https://cdn.iconscout.com/icon/free/png-256/jenkins-5-569553.png`

- 会获得一个重要的订阅链接，妥善保存，这个将在Jenkins高级篇用到

  ![image-20210415122455931](Mattermost.assets/image-20210415122455931.png)

- 最终效果：

![image-20210415193802769](Mattermost.assets/image-20210415193802769.png)

#### Mattermost配置Jenkins插件

- 在**团队设置 > 插件集市**找到Jenkins并安装
- 在配置页面填上`your_jenkins_url`，**重新生成**下面的`At Rest Encryption Key`
- 现在可以使用斜杠命令向jenkins发送命令：
  - 使用API_TOKEN（在Jenkins个人页中获取）连接Jenkins server：`/jenkins connect admin API_TOKEN`
  - 启用被禁用的Job：`/jenkins enable your_jobname`

- 最终效果：

  ![image-20210415214657978](Mattermost.assets/image-20210415214657978.png)