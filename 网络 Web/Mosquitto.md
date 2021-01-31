# Mosquitto

*summary*

<img src="Mosquitto.assets/image-20201224102149134.png" alt="image-20201224102149134" style="zoom:80%;" />

**Mosquitto**是一款开源的MQTT message broker（消息队列遥测传输的代理）服务，由Eclipse Foundation提供。

[官方网址](https://mosquitto.org/)

[MQTT node.js文档](https://www.npmjs.com/package/mqtt#client)

[MQTT JavaScript文档](https://github.com/mqttjs/MQTT.js)

[Paho.mqtt Python文档](https://github.com/eclipse/paho.mqtt.python)

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

---

*2020.12.27*

### 订阅SYS消息

> [mosquitto常见问题及其解决办法](https://blog.csdn.net/houjixin/article/details/46711547)
>
> [【官方文档】mosquitto SYS消息总览](https://mosquitto.org/man/mosquitto-8.html)

mosquitto可以发布一些关于自身状态的系统topic消息，启用及使用的步骤如下：

- `mosquitto.conf`182行附近找到配置`sys_interval 10`

- 客户端订阅SYS消息

  ```js
  client.subscribe('$SYS/broker/subscriptions/count');
  ```

### 如何断线重连时接收离线消息

> [MQTT中的mosquitto简单使用以及订阅离线消息](https://www.jianshu.com/p/da9bd6be72c2)

要想在客户端重连时马上收到离线时的消息，必须要确保两点：1）自身的client_id缓存在了MQ服务器上。2）消息的服务质量为“至少一次”或“只有一次”。

- **缓存客户端，持久会话**：启动客户端时设置客户端id和不清除客户端缓存，以接收离线消息

  - js：

    ```js
    const options = {
          clientId: 'mina/1',
          clean:false
    };
    const client = mqtt.connect(host, options);
    ```

  - python：

    ```python
    # clean_session为False时，会在重连时收到qos>0的所有消息
    client = mqtt_client.Client(
        client_id="printer-A", clean_session=False,transport='websockets')
    ```

- **订阅/发布Qos>=1的消息**：在订阅或发布时可以附带服务质量（Qos）的参数设置

  - js：

    ```js
    client.subscribe('mobile/111', {qos: 1});
    client.publish('printer/aaa', "Ok", {qos: 1})
    ```

  - python：

    ```python
    client.subscribe("printer/aaa", qos=1)
    client.publish("mobile/111", "Ok", qos=1)
    ```

注意clean_session=false时可以主动接收flag参数，如果为true，说明此次连接为重连，会获取存储的缓存信息（**包括**之前的订阅主题，但由于默认重新订阅会不断覆盖，所以不会出现重复订阅导致消息重复的问题）。

### 奇怪！每隔120s就重连一次！Socket error on...错误

> [Nginx代理webSocket经常中断的解决方法（也就是如何保持长连接）](https://blog.csdn.net/u011411069/article/details/98475433)

初次使用IP连接时，并没有出现这种情况。之后对mosquitto服务配置了域名后，客户端client才出现了频繁重连的情况，那么基本可以确定是网络相关的配置出现问题。

- 首先需要nginx来帮助frp作更细致的反向代理配置，这里主要是增加了ws的支持和增加了超时时间：

  ```nginx
  #PROXY-START/
  location  ~* \.(php|jsp|cgi|asp|aspx)$
  {
      proxy_pass http://127.0.0.1:23456;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header REMOTE-HOST $remote_addr;
      proxy_connect_timeout 4s;                #配置点1
      proxy_read_timeout 120s;          #配置点2，如果没效，可以考虑这个时间配置长一点
      proxy_send_timeout 12s;                  #配置点3
      proxy_set_header Upgrade $http_upgrade; 
      proxy_set_header Connection "Upgrade";
  }
  location /
  {
      proxy_pass http://127.0.0.1:23456;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header REMOTE-HOST $remote_addr;
      
      add_header X-Cache $upstream_cache_status;
      
      proxy_connect_timeout 4s;                #配置点1
      proxy_read_timeout 120s;          #配置点2，如果没效，可以考虑这个时间配置长一点
      proxy_send_timeout 12s;                  #配置点3
      proxy_set_header Upgrade $http_upgrade; 
      proxy_set_header Connection "Upgrade";
      #Set Nginx Cache
      
      	add_header Cache-Control no-cache;
      expires 12h;
  }
  
  #PROXY-END/
  ```

- 然后就是mosquitto的使用过程中增加心跳包发送，规定每隔30s就发送一次心跳包

  - js：

    ```js
    const options = {
          keepalive:30
    };
    const client = mqtt.connect(host, options);
    ```

  - python：

    ```python
    client.connect(host=HOST,port=PORT,keepalive=30)
    ```

---

*2020.12.28*

### 施工中：客户端如何根据收到的消息来分发事件

> [*Getting Started with Node.js and MQTT*](https://blog.risingstack.com/getting-started-with-nodejs-and-mqtt/)

### 施工中：及时处理异常

如果在处理消息时产生异常并引起程序中断，消息会继续保留直到处理完毕（存疑），就像车祸现场一直无人处理，导致后面的车辆排长队。这会导致之后的消息不断阻塞。

---

*2021.01.02*

### token认证

> [MQTT服务器搭建--Mosquitto用户名密码配置](https://blog.csdn.net/u012377333/article/details/69397124)

Mosquitto在部署到公网上之后，为了防止其他人随意的连接，可以增加用户名密码的认证配置。

- 首先是开启认证功能，Mosquitto服务器的配置文件里增加以下几行（668行附近）：

  ```yaml
  allow_anonymous false # 不允许匿名
  password_file /mosquitto/pwfile # 存放密码文件的路径
  ```

- 然后进入到mosquitto环境中，执行命令`mosquitto_passwd -c /mosquitto/pwfile your_username`，随后输入密码`your_password`。

- 由于之前使用的docker方式安装，所以还要把生成的文件挂载出来，修改`docker-compose.yml`

  ```yaml
  volumes:
      - ./data:/mosquitto/data
      - ./log:/mosquitto/log
      - ./config:/mosquitto/config
      - ./pwfile:/mosquitto/pwfile
  ```

  - 记得`docker cp mosquitto:/mosquitto/pwfile .`把密码文件取出来

- 最后docker-compose来重启容器即可。

- 客户端就需要在连接的选项中配置账密认证。

  ```js
  const options = {
      username:"your_username",
      password:"your_password"
  };
  const client = mqtt.connect(host, options);
  ```

  ```python
  client.username_pw_set(username="your_username",password="your_password")
  client.connect(host=HOST, port=PORT)
  ```

---

*2021.01.05*

### MQTT+nginx的必要配置

> [MQTT微信小程序开发工具可以连接真机不能连接问题解决](https://www.freesion.com/article/9239220721/)

Mosquitto服务器在给微信小程序真机使用时，发现真机无法连接而开发者工具却可以。经过对请求头的比对可以发现`Sec-WebSocket-Protocol`有区别（左边是真机调试，右边是开发者工具）：

<img src="Mosquitto.assets/image-20210105184053103.png" alt="image-20210105184053103" style="zoom:80%;" />

要解决这个问题就得手动添加Headers的这一参数，在mqtt服务器的nginx配置文件中：

```nginx
location /{
    proxy_pass http://iot.xxxx.com:9001;
    #代理到上面的地址去，格式：http://域名:端口号，
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
    proxy_set_header Sec-WebSocket-Protocol mqtt;
    proxy_connect_timeout 5s; #配置点1
    proxy_read_timeout 60000s; #配置点2，如果没效，可以考虑这个时间配置长一点
    proxy_send_timeout 60000s; #配置点3
}
```

---

*2021.01.30*

### 施工中：感知客户端上线下线

> [Mosquitto感知客户端上下线的方法](https://blog.csdn.net/weixin_43937302/article/details/95769728)

### 施工中：部署WSS

> [Mosquitto SSL Configuration -MQTT TLS Security](http://www.steves-internet-guide.com/mosquitto-tls/)
>
> [MQTT服务-Mosquitto简单安装及TLS双向认证配置](https://www.cnblogs.com/juanjuankaikai/p/11425598.html)
>
> [配置HTTPS全过程](https://blog.csdn.net/csdn_bbc/article/details/81536865)

1. 新建一个**CA公钥/私钥对**
   
- `openssl genrsa -des3 -out ca.key 2048`
   - 需要输入4至1023位的密码保护，如`1234`
   
2. 新建一个**CA证书**并使用步骤1的**key**
   
   - `openssl req -new -x509 -days 1826 -key ca.key -out ca.crt`
   - 可以输入一些地区信息
   
3. 为**Mqtt代理**新建一个**代理公钥/私钥对**，记得不要使用密码保护

   - `openssl genrsa -out server.key 2048`

4. 新建一个**Mqtt代理的证书**并使用步骤3生成的**key**

   - `openssl req -new -out server.csr -key server.key`
   - 输入**Common Name**的信息时需要格外留意，一般会填写Mqtt服务器的域名或IP地址。**重要的是这个信息将用于之后的配置**。填`mqtt`也无所谓。
   - 我们不需要把这个证书发给CA认证，**因为我们就是CA**！

5. 用**CA证书**认证步骤4生成的**Mqtt代理的证书**

   - `openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 360`

6. 将**CA证书**，**Mqtt代理的证书**，**代理公钥/私钥对**的相关文件全部放在**Mqtt服务器**上的一个文件夹中，如`/certs`

   - 现在当前路径下会有几个密钥文件，其中框起来的3个文件是关键文件`ca.crt`，`server.crt`，`server.key`

   <img src="Mosquitto.assets/image-20210131111330676.png" alt="image-20210131111330676" style="zoom:80%;" />

   - 将它们拷贝到Mqtt服务端的一个文件夹，如`/mosquitto/ca_certs`

7. 将**CA证书**拷贝到**Mqtt客户端**

   - 将`ca.crt`

8. 编辑Mosquitto配置文件以使用上述文件

9. 修改**Mqtt客户端**的代码以使用Https/Wss连接

