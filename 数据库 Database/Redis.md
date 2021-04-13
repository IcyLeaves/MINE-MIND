# Redis

*summary*

<img src="https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=2187486875,447854113&fm=26&gp=0.jpg" alt="img" style="zoom:80%;" />

**Redis**是一个开源key-value存储系统。

---

*pinned*

> [Windows安装Redis并添加本地自启动服务](https://www.cnblogs.com/hongmoshui/p/10621814.html)

- [建议下载zip压缩包](https://github.com/microsoftarchive/redis/releases)
- msi安装后在`C:\Program Files\Redis`找到目录
- 启动`redis-server.exe`

---

*2021.01.29*

### Windows Redis开机自启动

> 这个问题并未完全解决，以下草稿仅供参考，很可能有杂乱/缺失/错误等情况

- 启动`redis-server.exe`

- 我的电脑 右键 > 管理 > 服务 > Redis 右键 > 

---

*2021.04.08*

### redis常用命令

在`redis-cli.exe`所在目录：

- 连接到远程redis：`redis-cli -h redis.example.com -p 8080`

进入redis后：

- 认证：`auth YOUR_PASSWORD`
- 查看所有的键：`keys *`
- 删除**当前数据库**所有的键：`flushdb `