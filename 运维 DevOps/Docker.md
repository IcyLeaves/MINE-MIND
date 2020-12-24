# Docker

*summary*

<img src="Docker.assets/image-20201224231344749.png" alt="image-20201224231344749" style="zoom:80%;" />

**Docker**是一个容器管理工具。

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