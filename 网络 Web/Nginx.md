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

### 施工中：504 Gateway Time-out

---

*2021.01.05*

### 常用命令

- `nginx -s reload`：重载配置（有时候可以替代重启）