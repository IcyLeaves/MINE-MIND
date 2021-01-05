# GitLab

*summary*

<img src="..\template.assets\image-20201223131536639.png" alt="image-20201223131536639" style="zoom:25%;" />

**GitLab**是一个代码仓库。

---

*pinned*

### Git安装

- `yum -y install git`

### 安装方式

> [docker-compose搭建gitlab并配置邮箱](https://blog.csdn.net/u014691098/article/details/106972399/)
>
> [基于docker-compose搭建gitlab](https://www.cnblogs.com/sonyy/p/13150691.html)

- 推荐安装Omnibus的GitLab CE的Docker版本，这边提供了一步式docker-compose.yml文件示例

  - 初次运行请执行：

    ``` sh
    $ sudo docker run --detach \
      --publish 23456:80\
      --name gitlab \
      --restart always \
      --volume /your-workstation/gitlab/config:/etc/gitlab \
      --volume /your-workstation/gitlab/log:/var/log/gitlab \
      --volume /your-workstation/gitlab/data:/var/opt/gitlab \
      gitlab/gitlab-ce:latest
    ```

  - 然后使用的docker-compose文件：

    ```yml
    version: '3.6'
    
    services:
      gitlab:
        restart: always
        image: 'gitlab/gitlab-ce:latest'
        environment:
          TZ: 'Asia/Shanghai'
          GITLAB_OMNIBUS_CONFIG: |
            external_url 'http://10.11.6.128:8880'
        ports:
          - "8880:80"
        volumes:
          - ./config:/etc/gitlab
          - ./log:/var/log/gitlab
          - ./data:/var/opt/gitlab
        privileged: true
    ```

---

*2021.01.03*

### 常用的调试命令

> [GitLab常用命令](http://www.chenxm.cc/article/708.html)

许多时候GitLab会发生意想不到的错误，这时候要进入容器内部执行一些调试命令行来排查。

- 检查配置：`gitlab-ctl check-config`
- 查看状态：`gitlab-ctl status`
- 查看运行时日志：`gitlab-ctl tail`
- 重新应用配置：`gitlab-ctl reconfigure`
- nginx日志：`cd /var/log/gitlab/nginx`和`cat gitlab_error.log`

### 施工中：[execute] fail: gitaly: runsv not running

https://blog.csdn.net/jia12216/article/details/88352711

[502](https://www.cnblogs.com/sonyy/p/13150691.html)

[【Git学习】解决GitLab内存消耗大的问题](https://ouyangpeng.blog.csdn.net/article/details/84066417)