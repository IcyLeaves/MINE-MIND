# +Go

*summary*

![img](Go.assets/u=1108492412,3468081374&fm=26&fmt=auto&gp=0.jpg)

**Go**是一个开源的编程语言

---

*pinned*

### Linux安装Go编译环境

- 下载Go编译器，[Download and install - The Go Programming Language (golang.org)](https://golang.org/doc/install)
- 中国代理：[go切换proxy中国代理 - Awakenedy - 博客园 (cnblogs.com)](https://www.cnblogs.com/awakenedy/articles/12813936.html)

### Linux上VS Code配置Go语言插件

> [Go项目的目录结构 - 迪克猪 - 博客园 (cnblogs.com)](https://www.cnblogs.com/zsy/p/5262632.html)
>
> [vscode安装go所有插件（全网最全）_夏的博客-CSDN博客](https://blog.csdn.net/xiazhipeng1000/article/details/109584712)
>
> [vs code golang开发 代理设置, 告别手动下载工具包_万象博客-CSDN博客](https://blog.csdn.net/jpy1391/article/details/107165366/)
>
> [gometalinter-golang代码质量检查分析工具 - SegmentFault 思否](https://segmentfault.com/a/1190000013553309?utm_source=tag-newest)
>
> [gometalinter cannot be installed with go 1.11 module support enabled · Issue #521 · alecthomas/gometalinter (github.com)](https://github.com/alecthomas/gometalinter/issues/521)

- 如果在VS Code安装完Go扩展，会提示下载一些组件包，此时就会受到不可描述的阻碍

![image-20210609151217408](Go.assets/image-20210609151217408.png)

- 如果在Go1.13 及以上，修改代理是最方便的方法
- 只需先在终端执行`export GO111MODULE=on`和`export GOPROXY=https://goproxy.cn`，然后再**Install All**即可
- 另外还需要安装的是`GO111MODULE=off go get -u -v github.com/alecthomas/gometalinter`及`GO111MODULE=off gometalinter --install --force`，这个工具能扫描项目，使得vendor文件夹也有作用
  - `gometalinter ./...`

---

*2021.04.27*

### 为net/http设置代理

> [Setting up proxy for HTTP client - Stack Overflow](https://stackoverflow.com/questions/14661511/setting-up-proxy-for-http-client)
>
> [Error when fetching URL through proxy in Go - Stack Overflow](https://stackoverflow.com/questions/14669958/error-when-fetching-url-through-proxy-in-go)
>
> [使用Privoxy将socks5代理转为http代理_看写写-CSDN博客](https://blog.csdn.net/li740207611/article/details/52045471)
>
> [The Go Playground (golang.org)](https://play.golang.org/p/l0iLtkD1DV)
>
> [linux使用privoxy进行PAC代理 - 起风了 (dyxmq.cn)](https://www.dyxmq.cn/linux/pac-with-privoxy.html)
>
> [Ubuntu 18.04 配置privoxy代理_wangwang的博客-CSDN博客](https://blog.csdn.net/weixin_37532614/article/details/104761903)
>
> [[工具使用\] privoxy 实现 PAC 请求过滤-上地信息-shangdixinxi.com](http://shangdixinxi.com/detail-1349157.html)

- net/http会使用`HTTP_PROXY`和`HTTPS_PROXY`这两个环境变量作为默认的代理方式，因此导入环境变量可以使该系统上的所有net/http都使用该代理地址进行网络请求，是一个全局的方式。支持socks5的协议

  ```shell
  export HTTP_PROXY=http://123.456.789.00:1080
  export HTTPS_PROXY=socks5://123.456.789.00:1081
  ```

- 如果你希望对局部域名使用代理，你可以在代理服务器上创建PAC规则，这里使用privoxy的方式。

  - 假如机器上已经开启了socks5代理：`socks5://127.0.0.1:1080`

  - 首先安装privoxy

    ```shell
    apt-get -y install privoxy
    ```

  - 之后添加PAC自定义规则，添加文件`/etc/privoxy/pac.action`。这里default就说明不使用代理，pac就是使用了代理，当然如果你希望默认走代理而特殊域名不走代理，可以置换一下。

    ```shell
    {{alias}}
    default    = +forward-override{forward .} 
    pac     = +forward-override{forward-socks5 127.0.0.1:1080 .}
     
    {default}
    /
     
    {pac}
    .google.com
    ```
    
    - privoxy的配置文件在`/etc/privoxy/config`。搜索`listen-address`可以看到`listen-address  127.0.0.1:8118`，说明我们的代理地址已经监听在`http://127.0.0.1:8118`了。修改为`listen-address 0.0.0.0:8118`表示开放在局域网，不然其他机器访问会出现`connection refused`情况。`debug`代表日志等级，`cat /var/log/privoxy/logfile`查看日志。`actionsfile`代表应用的pac规则文件，需要注释其他默认规则。
    
    ```python
    #forward-socks5t / 127.0.0.1:1080 .
    
    debug 1
    debug 2
    debug 4
    
    #actionsfile match-all.action # Actions that are applied to all sites and maybe overruled later on.
    #actionsfile default.action   # Main actions file
    #actionsfile user.action      # User customizations
    actionsfile pac.action
    
    #filterfile default.filter
    #filterfile user.filter      # User customizations
    
    listen-address  0.0.0.0:8118
    listen-address  [::1]:8118
    ```
    
  - 最后重启代理服务，并设置开机启动

    ```shell
    service privoxy enable
    service privoxy restart
    ```

---

*2021.06.09*

### 报错module declares its path as A but was required as B

> [go mod module declares its path as: gtihub.com/xxx-xx but was required as:xx-xx_liuqun of program life-CSDN博客](https://blog.csdn.net/liuqun0319/article/details/104054313)	

- 报错格式是

```
...: ...@v1.2.3: parsing go.mod:
        module declares its path as: A
                but was required B
```

- 在当前go.mod中加入一行

```go
replace A => B v1.2.3 // indirect
```

