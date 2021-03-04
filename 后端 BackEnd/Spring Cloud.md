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

