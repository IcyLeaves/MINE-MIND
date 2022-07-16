# Spring Cloud

*summary*

<img src="./Spring Cloud.assets/u=4247047143,2462685194&fm=26&gp=0.jpg" alt="u=4247047143,2462685194&fm=26&gp=0" style="zoom:80%;" />

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


---

*2021.05.10*

### 接受FormData的数据（多个文件）

> 这个问题并未完全解决，以下草稿仅供参考，很可能有杂乱/缺失/错误等情况

> [前端vue ElementUI upload 上传文件，后端 Spring Boot MultipartFile接收文件存储_Another-CSDN博客](https://blog.csdn.net/qq_40522155/article/details/104887869)
>
> [Spring Boot + Vue 前后端分离，两种文件上传方式总结 - 江南一点雨 - 博客园 (cnblogs.com)](https://www.cnblogs.com/lenve/p/10782774.html)
>
> [JAVA项目同时上传多个文件和多个内容_凌大大的博客-CSDN博客_java多文件上传](https://blog.csdn.net/wohaqiyi/article/details/79179600)
>
> [spring boot 的request.getServletContext().getRealPath路径获取问题_Kenneth-peng-CSDN博客](https://blog.csdn.net/yongjiutongmi53151/article/details/87966767)
>
> [FormData上传文件 - 简书 (jianshu.com)](https://www.jianshu.com/p/51188659d778)
>
> [JSON_parseObject_InputStream_cn · alibaba/fastjson Wiki (github.com)](https://github.com/alibaba/fastjson/wiki/json_parseobject_inputstream_cn)
>
> [springboot项目获取resources路径（相对路径）_maybe-not的博客-CSDN博客](https://blog.csdn.net/qq_29669265/article/details/89678077)
>
> [Java Response实现文件下载_田野上的风筝-CSDN博客_response下载文件](https://blog.csdn.net/weixin_43100896/article/details/89880596)

---

*2021.07.21*

### 解析XML字符串

```java
import org.dom4j.*;

Document document = DocumentHelper.parseText(xmlStr);
Element root = document.getRootElement();

String something=root.selectSingleNode("/first/second").
```

---

*2021.10.1*

### 为Spring Cloud微服务配置JUnit5

> [Springboot集成JUnit5优雅进行单元测试 - 海向 - 博客园 (cnblogs.com)](https://www.cnblogs.com/haixiang/p/13812363.html)
>
> [单元测试实践（SpringCloud+Junit5+Mockito+DataMocker） - 陈晨_软件五千言 - 博客园 (cnblogs.com)](https://www.cnblogs.com/pluto4596/p/11703382.html)

#### 一、安装

安装的步骤就过了吧

#### 二、idea配置test文件夹

在idea中，**File** > **Project Structure** > **Modules**，然后在你预先创建的test文件夹上右键，选择test就行了。一般可以按照这样的结构创建test：

<img src="Spring Cloud.assets/image-20211001182542248.png" alt="image-20211001182542248" style="zoom:80%;" />

#### 三、创建一个测试类

在idea中，在某个实现类的代码里右键，**Generate..** > **Test**，就能创建一个最简单的文件

#### 四、跑第一个测试

这是最困难的一步，因为如果进行单元测试，往往会隔断很多中间件的联系，从而导致测试根本跑不起来。

```java
@ExtendWith(SpringExtension.class)
@SpringBootTest
@ActiveProfiles("dev")
@TestPropertySource(locations = "classpath:application.yml")
@WebAppConfiguration
public class CourseServiceImplTest {
    @Autowired
    ICourseService iCourseService;
    @Test
    void allCourse() {
        //示例
        Map<String,Object> res=iCourseService.allCourse(1,10);
        assertEquals(10,res.keySet().size());
    }
}
```

---

*2022.06.29*

### 如何编写Wrapper&强大的Mapper

> 参考链接：[Mybatis plus强大的条件构造器QueryWrapper条件构造器基础方法解释](https://www.cnblogs.com/nongzihong/p/12661446.html)

#### 单表、条件查询

``` sql
SELECT *
FROM app_user
WHERE company_id="aaa";
```

```java
QueryWrapper<ClientUser> queryWrapper=new QueryWrapper<>();
queryWrapper.lambda().eq(ClientUser::getCompanyId, "aaa");

Page<BoxInfo> boxInfoPage=new Page<>(currentPage,pageSize);
boxInfoPage = boxInfoMapper.selectPage(boxInfoPage,queryWrapper);
```

#### 单表、多行更新

``` sql
UPDATE box_info
SET user_id="aaa"
WHERE serial_num IN ["NO1","NO2","NO4"];
```

```java
String strArray = {"NO1","NO2","NO4"};
List<String> strList = Arrays.asList();

UpdateWrapper<BoxInfo> updateWrapper=new UpdateWrapper<>();
updateWrapper.lambda().
        in(BoxInfo::getSerialNum,strList).
        set(BoxInfo::getUserId,"aaa");

int updated=boxInfoMapper.update(null,updateWrapper);
```

#### 多表、多行、条件查询、可选查询、分页查询

```sql
SELECT *
FROM box_info as B
LEFT JOIN app_user as U
ON B.user_id=U.id
WHERE B.is_deleted="0" and U.is_deleted="0" and B.company_id="aaa" (and B.user_id="bbb");
```

``` java
@Mapper
public interface BoxInfoMapper extends BaseMapper<BoxInfo> {
    List<MyBoxRes> AllMyBox(String companyId,String userId);
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.xxx.admin.mapper.PrinterMapper">
    <select id="AllMyBox" resultType="com.cloud.response.MyBoxRes">
        select *
        from box_info as B
        left join app_user as U on B.user_id=U.id
        <where>
            B.is_deleted="0" and U.is_deleted="0" and B.company_id=#{companyId}
            <if test="userId != '' and userId != null">
                    and B.user_id=#{userId}
            </if>
        </where>
    </select>
</mapper>
```

```java
List<MyBoxRes> boxList=boxInfoMapper.AllMyBox(companyId,userId);//先返回所有匹配数据
boxList=PageUtil.startPage(boxList,currentPage,pageSize);//手动分页
```

#### 多表、多行、条件查询、分页查询、聚合函数+GROUPBY

```sql
SELECT U.id,U.company_id,U.truename,U.phone,
COUNT(case when B.is_deleted=0 then B.serial_num end) as boxNum,
IFNULL(SUM(case when P.is_deleted=0 then
  (P.print_a4_black_num+
  P.print_a3_black_num+
  P.print_a4_color_num+
  P.print_a3_color_num+
  P.copy_a4_black_num+
  P.copy_a3_black_num+
  P.copy_a4_color_num+
  P.copy_a3_color_num) else 0 end) ,0) as printNum
FROM app_user as U
LEFT JOIN box_info as B ON U.id=B.user_id
LEFT JOIN printer_info as P ON B.serial_num=P.box_sn
WHERE U.title='客户' and U.company_id="icyTest" and U.is_deleted=0
GROUP BY U.id;
```

```xml
<select id="AllMyClient" resultType="com.cloud.response.MyClientRes">
    SELECT U.id,U.company_id,U.truename,U.phone,
    COUNT(case when B.is_deleted=0 then B.serial_num end) as boxNum,
    IFNULL(SUM(case when P.is_deleted=0 then
    (P.print_a4_black_num+
    P.print_a3_black_num+
    P.print_a4_color_num+
    P.print_a3_color_num+
    P.copy_a4_black_num+
    P.copy_a3_black_num+
    P.copy_a4_color_num+
    P.copy_a3_color_num) else 0 end) ,0) as printNum
    FROM app_user as U
    LEFT JOIN box_info as B ON U.id=B.user_id
    LEFT JOIN printer_info as P ON B.serial_num=P.box_sn
    WHERE U.title='客户' and U.company_id=#{companyId} and U.is_deleted=0
    GROUP BY U.id;
</select>
```