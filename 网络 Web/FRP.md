# FRP

*summary*

<img src="FRP.assets/image-20201224101638100.png" alt="image-20201224101638100" style="zoom:60%;" />

**FRP**是一个反向代理应用，全名叫Fast Reverse Proxy。主要用于内网穿透，从而可以访问公网服务器时访问到内网的服务器上。

---

*2020.12.24*

### 穿透

目标是，能通过域名`a.example.com`访问内网服务器B的`12345`端口的服务Serv。

- 首先做好准备工作
  - 一台公网服务器A
  - 需要穿透的内网服务器B
- 接下来，我们要让`a.example.com`可以被任何人访问
  
- 一般是通过公网服务器A的运营商，添加域名配置或DNS解析
  
- 然后，建立frp服务端

  - 在公网服务器A上配置frp服务端，编辑frps.ini

    ``` ini
    [common]
    port=9999 //frp服务的端口
    token=myfrp //服务端和客户端的认证凭证
    vhost=80 //转发端口，即用户访问:80->:9999
    ```

- 建立frp客户端

  - 在内网服务器B上配置frp客户端，编辑frpc.ini

    ``` ini
    [common]
    port=9999 //frp服务的端口
    token=myfrp //服务端和客户端的认证凭证
    
    [my.service.one] //名字任取
    type = http
    local_ip = 127.0.0.1
    local_port = 23456 //Serv服务在内网的端口
  custom_domains = a.example.com //和访问的域名相同
    ```

- 如果配置文件修改了，记得重启frp服务
  - 客户端：`systemctl restart frpc.service`
  - 服务端：`systemctl restart frps.service`

大功告成！现在`a.example.com`相当于`B.ip.address:23456`

---

*2021.01.01*

### 解决启动frpc报错Unit frpc.service entered fail state.

- 检查frps是否开启。
- 检查frps的安全组是否已放行frp端口及所有穿透端口。
- 检查frps和frpc版本是否匹配。

---

*2021.01.31*

### 单独使用FRP进行HTTPS转HTTP

> [frp下反向代理实现https协议](https://www.cnblogs.com/shook/p/12790532.html)

- FRP服务端编辑：`frps.ini`

```ini
vhost_https_port = 443
```

- FRP客户端申请SSL证书
- FRP客户端编辑：`frpc.ini`

```ini
[web_https]
type = https
custom_domains = test.domains.cn

# 以下为https新加的内容
plugin = https2http
plugin_local_addr = 127.0.0.1:{your_web_port}

#证书相关配置
plugin_crt_path = {your_SSL_crt_dir}/fullchain.pem
plugin_key_path = {your_SSL_key_dir}/privkey.pem
plugin_host_header_rewrite = 127.0.0.1 #这里可以重写请求的域名
plugin_header_X-From-Where = frp
```

