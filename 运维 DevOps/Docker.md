# Docker

*summary*

<img src="Docker.assets/image-20201224231344749.png" alt="image-20201224231344749" style="zoom:80%;" />

**Docker**是一个容器管理工具。

---

*pinned*

### 下载Docker-CE

> [Install Docker Engine on CentOS](https://docs.docker.com/engine/install/centos/)

### 下载Docker-Compose

> [官方文档](https://docs.docker.com/compose/install/)

- Linux的下载方式：
  - 下载文件：`sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`
  - 赋予权限：`sudo chmod +x /usr/local/bin/docker-compose`
  - 建立软连接：`sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose `
  - 开启Docker服务：`service start docker`
  - 设置开机自启动：`systemctl enable docker`

### 卸载Docker-Compose

> [docker-compose的安装和卸载](https://www.cnblogs.com/codehui/p/docker-compose-install.html)

- 直接删除二进制文件：`sudo rm /usr/local/bin/docker-compose`

---

*2020.12.24*

### Docker Compose挂载目录的限制和解决办法

在使用docker-compose.yml挂载目录时...

```yaml
version: '3.6'

services:
  web:
    volumes:
      - ./data:/usr/data                        
```

宿主机文件目录会挂载到容器内文件目录，文件也是双向同步的。但有几条很重要的规则是：

- 启动镜像阶段会执行一次文件挂载
- 如果宿主机不存在该目录，会新建**空的**文件夹
- 然后将宿主机目录的内容**覆盖**容器内的内容

这会导致第一次运行时容器内对应的挂载目录全部清空。

**解决办法**是第一次先采用`docker run -dit`的方式运行镜像，然后执行`docker cp`命令手动将容器内的文件拷贝到宿主机上。之后就可以正常使用了。

---

*2021.01.01*

### Docker常用命令

- `docker logs {container_id} --tail 100 -f -t`
  - `--tail 100`：显示末尾100行
  - `-f`,`--follow`：跟踪实时日志
  - `-t`,`--timestamp`：显示时间戳

---

*2021.01.04*

### ERROR: failed to register layer: Error processing tar file(signal: killed): 

---

