# Spring Cloud

*summary*

<img src="./Spring.assets/u=4247047143,2462685194&fm=26&gp=0.jpg" alt="u=4247047143,2462685194&fm=26&gp=0" style="zoom:80%;" />

**Spring Cloud**是一个示例markdown文件，帮助作者快速建立更多的知识库！

一个知识库至少要包含以下内容：

- **主体**：在一级标题处显示。
- **概要**：紧跟一级标题显示。介绍主体的大致用途和一些关键点。
- **图标**：在概要中居中显示。
- **正文**：用分隔线分隔块、每一块以日期带头记录一天之内的知识。

---

*2021.02.06*

### 解码Base64

> [Java如何进行Base64的编码(Encode)与解码(Decode)](https://www.cnblogs.com/alter888/p/9140732.html)

```java
final Base64.Decoder decoder = Base64.getDecoder();
final Base64.Encoder encoder = Base64.getEncoder();
final String encodedText = "假设这是一个Base64编码";
//解码
System.out.println(new String(decoder.decode(encodedText), "UTF-8"));
```

### Json字符串转Map<String,Object>

> [fastjson对象，JSON，字符串，map之间的互转](https://www.cnblogs.com/heqiyoujing/p/9840424.html)

- 需要使用alibaba的fastjson，`import com.alibaba.fastjson.JSON;`

```java
//String 转 Json对象
JSONObject jsonObject = JSONObject.parseObject(jsonString);
//json对象转string
String jsonString = jsonObject.toJSONString();
```

---

*2021.03.29*

### 解决windows下端口占用问题

> [8080：The Tomcat connector configured to listen on port 8080 failed to start 的解决办法](https://blog.csdn.net/weixin_44259720/article/details/104521766)

- 这个问题是碰到启动Gateway时，报错Failed to start component [Connector[HTTP/1.1-8080]]发现的
- 打开cmd
  - `netstat -ano`：查看所有端口和PID。找到[::]:8080对应的PID
  - `tasklist | findstr "{找到的PID}"`：看看是哪个进程占用
  - `taskkill /f /t /im {进程名}`：杀死该进程

### Spring Cloud+zuul网关解决跨域问题

> [jQuery的官方文档](https://api.jquery.com/jquery.ajax/)
>
> [跨域请求的OPTIONS问题](https://blog.csdn.net/achang07/article/details/79380990)
>
> [不要再问我跨域的问题了](https://segmentfault.com/a/1190000015597029)
>
> [10种跨域解决方案（附终极大招）](https://juejin.cn/post/6844904126246027278#heading-34)

跨域问题往往和前后端都有关系，这里介绍的是Spring Cloud+zuul网关组合下的后端解决方案

- 本次问题是由于前端使用jQuery.ajax发起的跨域请求：

  ```js
  $.ajax({
      type: "POST",
      dataType: "json",
      url: "http://localhost:8080/api/sys/login",
      data: {"a":1},
      contentType: "application/json",
  });
  ```

  - 报出错误

    ![image-20210329121705745](Spring Cloud.assets/image-20210329121705745.png)

  - jQuery的官方文档有对跨域的详细说明，若`contentType`设置为`application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain`以外的其他值，会使浏览器先发送一个OPTIONS请求

- 对策1：在服务端的`Response Header`加上`Access-Control-Allow-Headers("*")`。这里是在zuul-gateway写的类。

  ```java
  @Configuration
  public class CrossDomainConfig {
      @Bean
      public CorsFilter corsFilter() {
          final UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
          final CorsConfiguration config = new CorsConfiguration();
          config.setAllowCredentials(true); // 允许cookies跨域
          config.addAllowedOrigin("*");// #允许向该服务器提交请求的URI，*表示全部允许
          config.addAllowedHeader("*");// #允许访问的头信息,*表示全部
          config.setMaxAge(18000L);// 预检请求的缓存时间（秒），即在这个时间段里，对于相同的跨域请求不会再预检了
          config.addAllowedMethod("*");// 允许提交请求的方法，*表示全部允许
          source.registerCorsConfiguration("/**", config);
          return new CorsFilter(source);
      }
  }
  ```

  - 这样可以解决大部分的跨域请求，但没办法处理POST + application/json

- 对策2：前端改成简单请求`text/plain`，不推荐这种方法

  ```js
  $.ajax({
      type: "POST",
      dataType: "json",
      url: "http://localhost:8080/api/sys/login",
      data: JSON.stringify({"a":1}),
      contentType: "text/plain",
  });
  ```

  - 缺点1：后端需要手动添加解析JSON的代码
  - 缺点2：数据将明文传输

- 对策3：OPTIONS请求放行。复杂请求的OPTIONS可能被过滤了

  ```java
  @Override
  public boolean shouldFilter() {
      RequestContext requestContext = RequestContext.getCurrentContext();
      HttpServletRequest request = requestContext.getRequest();
      if (request.getMethod().equals(RequestMethod.OPTIONS.name())) {
          log.info("OPTIONS请求不做拦截操作");
          return false;
      }
      return true;
  }
  ```

  

