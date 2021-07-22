# Nginx

*summary*

<img src="./Nginx.assets/image-20201229111812766.png" alt="image-20201229111812766" style="zoom:80%;" />

**Nginx**是一个高性能的HTTP和反向代理web服务器。

---

*2020.12.29*

### 配置项

- **post文件传输大小限制**：默认1M。

  ```nginx
  client_max_body_size 20m;
  ```

### 504 Gateway Time-out

> 这个问题并未完全解决，以下草稿仅供参考，很可能有杂乱/缺失/错误等情况

---

*2021.01.05*

### 常用命令

- `nginx -s reload`：重载配置（有时候可以替代重启）

---

*2021.01.31*

### HTTPS转HTTP（后续可配合FRP）

> [frp下反向代理实现https协议](https://www.cnblogs.com/shook/p/12790532.html)

- 对于需要配置https的网站：

```nginx
server {
        listen 80;                                                               # http对应端口
	listen 443 ssl;                                                          # https对应端口，
  	ssl_certificate /etc/letsencrypt/live/test.test.com/fullchain.pem;       # 证书存放位置
  	ssl_certificate_key /etc/letsencrypt/live/test.test.com/privkey.pem;     # 证书存放位置
        server_name *.test.test.com;                                             # ip，域名，我这里以泛域名举例，毕竟是做反向代理，http就不用配置了
                                                                                 # https还可以做其他安全配置，需要的去看其他文章
    location / {
        proxy_pass  http://127.0.0.1:12369;                                      # 映射的frp服务端frps.ini的 vhost_http_port端口
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_max_temp_file_size 0;
        proxy_redirect off;
        proxy_read_timeout 240s;
    }
                                                                                 # 我这里没做http强制转换为https，有需要的可以做一下
        error_page   500 502 503 504  /50x.html; 
  	location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

---

*2021.04.25*

### 验证静态文件

- 把需要验证的文件`abcdefg.txt`放置在`/static`目录下
- nginx添加配置

```nginx
location /abcdefg.txt{
    root /static;
}
```

- 访问`<your_server_url>/abcdefg.txt`时，就会访问文件`/static/abcdefg.txt`

- 还有一个更能说明原理地一个例子：

  ```nginx
  location /first/abcdefg.txt{
      root /static;
  }
  ```

  - 访问`<your_server_url>/first/abcdefg.txt`时，就会访问文件`/static/first/abcdefg.txt`，也就是root是将**域名部分（不含第一个斜杠后的部分）**替换为一个文件目录

