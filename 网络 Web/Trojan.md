# Trojan

*summary*

---

*pinned*

### Docker版安装

> [Docker 下快速部署Trojan-八零岁月 (80sy.com)](https://www.80sy.com/855.html)
>
> [teddysun/trojan - Docker Image | Docker Hub](https://hub.docker.com/r/teddysun/trojan)

- 在当前目录的trojan文件夹（可以自定义，然后和下面的volumes对应）下创建config.json。

  ```json
  {
      "run_type": "client",
      "local_addr": "0.0.0.0",
      "local_port": 1080,
      "remote_addr": "jp91r6.t3v.fun",
      "remote_port": 17500,
      "password": [
          "NItdc7"
      ],
      "log_level": 1,
      "ssl": {
  	"cert": "",
  	"verify": false,
  	"verify_hostname": false,
          "key": "/path/to/private.key",
          "key_password": "",
          "cipher": "ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384",
          "cipher_tls13": "TLS_AES_128_GCM_SHA256:TLS_CHACHA20_POLY1305_SHA256:TLS_AES_256_GCM_SHA384",
          "prefer_server_cipher": true,
          "alpn": [
              "http/1.1"
          ],
          "alpn_port_override": {
              "h2": 81
          },
          "reuse_session": true,
          "session_ticket": false,
          "session_timeout": 600,
          "plain_http_response": "",
          "curves": "",
          "dhparam": ""
      },
      "tcp": {
          "prefer_ipv4": false,
          "no_delay": true,
          "keep_alive": true,
          "reuse_port": false,
          "fast_open": false,
          "fast_open_qlen": 20
      },
      "mysql": {
          "enabled": false,
          "server_addr": "127.0.0.1",
          "server_port": 3306,
          "database": "trojan",
          "username": "trojan",
          "password": "",
          "cafile": ""
      }
  }
  ```

- docker-compose文件：第二个端口号与上面的config对应

  ```yaml
  version: "3.6"
  
  services:
    trojan:
      restart: always
      ports:
        - "21443:1080"
      volumes:
        - ./trojan:/etc/trojan
      image: teddysun/trojan
      container_name: trojan
  ```

- 测试：`curl ip.sb --socks5 127.0.0.1:21443 `，查看是否为代理IP

