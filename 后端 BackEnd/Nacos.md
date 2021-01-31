# Nacos

*summary*

<img src="./Nacos.assets/image-20210129130810872.png" alt="image-20210129130810872" style="zoom:80%;" />

**Nacos**是一个负责微服务发现、配置和管理的注册中心。

---

*pinned*

- [Windows下载zip文件](https://github.com/alibaba/nacos/releases)
- 默认用户名密码：`nacos`，`nacos`

---

*2021.01.29*

### Windows 本地启动nacos修改为默认单例模式

> [windows系统nacos1.3.2版本启动默认是集群模式，设置为单机默认](https://www.cnblogs.com/dingzt/p/13666101.html)

- 修改startup.cmd

```sh
# set MODE="cluster"
set MODE="standalone"
```

