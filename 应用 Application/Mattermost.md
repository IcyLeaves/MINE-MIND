# Mattermost

*summary*

<img src="./Mattermost.assets/图片123.png" alt="图片123" style="zoom:80%;" />

**Mattermost**是一个聊天工具

---

*2021.01.05*

### 多层nginx+挂载文件的配置修改之路

> 这个问题并未完全解决，以下草稿仅供参考，很可能有杂乱/缺失/错误等情况

web是nginx

dockerfile copy文件

entrypoint 软连接

还得手动修改/etc/nginx/conf.d然后nginx -s reload

下次停用web，因为外部已经有nginx代理  或者 研究透彻它的原理



