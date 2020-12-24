# Mosquitto

*summary*

<img src="Mosquitto.assets/image-20201224102149134.png" alt="image-20201224102149134" style="zoom:80%;" />

**Mosquitto**是一款开源的MQTT message broker（消息队列遥测传输的代理）服务，由Eclipse Foundation提供。

- 客户端可以自由订阅主题和发布消息
- 轻量级跨平台消息稳定，非常适合网络情况不佳的设备使用

---

*2020.12.24*

### MQTT+Websockets配置过程

- Mosquitto提供Docker镜像，初次运行时我们需要拷贝出一些默认的配置文件，具体原因参照[Docker Compose挂载目录的限制和解决办法](../运维%20DevOps/Docker.md/#docker-compose挂载目录的限制和解决办法)

  ``` shell
  docker run -dit eclipse-mosquitto
  docker cp [container_id]:/mosquitto/data /....../mosquitto/
  docker cp [container_id]:/mosquitto/log /....../mosquitto/
  docker cp [container_id]:/mosquitto/config /....../mosquitto/
  ```

- 接下来配合Docker Compose方法一套带走

  ```yaml
  version: '3.6'
  
  services:
    mosquitto:
      tty: true
      image: eclipse-mosquitto
      ports: # 冒号后面的端口建议不修改
        - "9001:9001" 
        - "1883:1883"
      volumes:
        - ./data:/mosquitto/data
        - ./log:/mosquitto/log
        - ./config:/mosquitto/config
  ```

- 在`./config/mosquitto.conf`中有一些关键的配置

  ```do
  port 9001
  protocol websockets
  listener 1883
  ```

  这样会在`1883`端口监听http消息，在`9001`端口监听ws消息

