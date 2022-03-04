# Linux

*summary*

<img src="Linux.assets/image-20201226183218723.png" alt="image-20201226183218723" style="zoom:80%;" />

**Linux**是一个常用的服务器系统

---

*2020.12.24*

### 释放磁盘空间

> [linux如何查看文件和目录大小](https://www.linuxprobe.com/linux-file-look.html)
>
> [“no space left on device”（磁盘空间不足）问题解决](https://blog.csdn.net/youmatterhsp/article/details/80382552)

**问题浮现：**磁盘空间不足的标志性报错为`no space left on device`

**了解情况：**

- `df -h`：查看磁盘使用情况
- `df -i`：查看inode（Linux文件索引）使用情况

**追根溯源：**

- `du -sh * | sort -nr | head`：查看当前目录占用空间最大的10个文件/子目录

### 计划任务同步时间

> [crontab与ntpupdate设置服务器时间同步](https://www.cnblogs.com/dyj0201/p/6477996.html)
>
> [crontab 详细用法、定时任务 、时间规则](https://blog.csdn.net/u013967628/article/details/83504839)

- 查看命令位置：`whereis ntpdate`。得到输出`/usr/bin/ntpdate`
- 立即执行**时间同步**命令：`/usr/bin/ntpdate pool.ntp.org   `
- 定时执行：

  - `crontab -e`进入编辑界面

  - 增加一行`*/5 * * * * /usr/bin/ntpdate pool.ntp.org` （每5分钟执行一次）

---

*2020.12.26*

### 查看所有被占用的端口

- `netstat -tunlp`，其中`-n`可以酌情取舍
- `lsof -i:3456`，查找在端口`3456`上打开的文件

---

*2021.01.04*

> 这个问题并未完全解决，以下草稿仅供参考，很可能有杂乱/缺失/错误等情况

>  [Linux查看配置文件中未被注释的有效配置行](https://www.cnblogs.com/gange111/p/9582637.html)

```
external_url 'http://10.11.6.128'
unicorn['worker_timeout'] = 90
unicorn['worker_processes'] = 3
unicorn['worker_memory_limit_min'] = "1024 * 1 << 20"
unicorn['worker_memory_limit_max'] = "1280 * 1 << 20"
 sidekiq['max_concurrency'] = 50
 sidekiq['min_concurrency'] = nil
```

---

*2021.01.05*

### SSH免密登录

> [Linux安全之SSH 密钥创建及密钥登录](https://blog.csdn.net/nahancy/article/details/79059135)

- 如果该台机器还未生成过ssh密钥，则`ssh-keygen -t rsa`来生成密钥在`/root/.ssh`。**如果有的话千万不要做这一步！！！**会覆盖掉旧密钥导致以前的认证全部需要重新执行。

  <img src="Linux.assets/image-20210105170138700.png" alt="image-20210105170138700" style="zoom:80%;" />

- 把公钥`id_rsa.pub`拷贝到需要登录的远程服务器上。

  - **方法一（推荐）：**使用`ssh-copy-id -i /root/.ssh/id_rsa.pub root@xxx.xx.xx.xx`
    
    - 会要求输入远程连接密码
  - 默认端口是22，需要更改时添加参数`-p 端口号`
    
  - **方法二：**手动追加公钥到`authorized_keys`**末尾**（`/root/.ssh/authorized_keys`）

    - 如果远程服务器还没有这个文件：

      ```sh
      touch authorized_keys
      chmod 600 /root/.ssh/authorized_keys
      ```
      
    - 追加的内容格式为`ssh-rsa 公钥内容 yourname@sample.com`

- 现在照常ssh就可以免密登录啦
  
  - 初次登录可能需要yes确认一下

---

*2021.03.04*

### VS Code远程SSH连接linux

> [使用 VS Code 远程连接Linux服务器进行实时开发](https://blog.csdn.net/weixin_42864357/article/details/105658765)

- 安装插件`Remote Development`

- 配置SSH连接

  - 点击左侧标签栏的**远程资源管理器**，选择小齿轮

    ![image-20210304101754185](Linux.assets/image-20210304101754185.png)

  - 在`C:\Users\...\.ssh\config`文件里编辑

    - `Host`：主机别名。随意填写。
    - `HostName`：主机地址。
    - `User`：登录的用户。

### 无法SSH连接到Ubuntu虚拟机

> [解决ubuntu 下 SSH无法连接的问题](http://blog.chinaunix.net/uid-69944074-id-5831708.html)

默认Ubuntu不安装ssh服务

- 安装ssh包：`sudo apt-get install ssh`
- 启动ssh服务：`sudo /etc/init.d/ssh start`\
- 修改ssh配置文件以允许root登录：`sudo vi /etc/ssh/sshd_config`，将`PermitRootLogin`的值修改为`yes`
- 保存后重启ssh服务：` service ssh restart`

---

*2021.04.08*

### Shell语法示例

#### if-else

```shell
if [ "$(curl -I -m 10 -o /dev/null -s -w %{http_code} www.baidu.com)" == "200" ]; 
then echo "yes"; 
else echo "no"; 
fi
```

上面语句的功能是，测试`www.baidu.com`的请求返回码是不是200，如果是输出`yes`，否则输出`no`

- `"$(your_command)"`：这种写法可以将里面的字符串作为一个命令去执行

---

*2021.04.10*

### 解压tar.gz

- 解压缩`*.tar.gz`：`tar -zxvf 文件名`

---

*2021.04.13*

### 修改设备名

- 设备名就是登录终端后`root@`后面的东西
- 方法1：
  - `vim /etc/sysconfig/network`
  - 修改`hostname=your_name`
  - 重启

- 方法2：
  - `vim /etc/hostname`
  - 重启

---

*2021.04.14*

### 更换apt-get源

> [apt-get update 一直 0% working 失败解决方案](https://www.cnblogs.com/xyzluck/p/13054837.html)

- 准备工作

  - 如果是宿主机，请务必**备份**`source.list`

- 替换镜像源

  - 查看一下原来的apt源，比如

    ```text
    # deb http://snapshot.debian.org/archive/debian/20210408T000000Z stretch main
    deb http://deb.debian.org/debian stretch main
    # deb http://snapshot.debian.org/archive/debian-security/20210408T000000Z stretch/updates main
    deb http://security.debian.org/debian-security stretch/updates main
    # deb http://snapshot.debian.org/archive/debian/20210408T000000Z stretch-updates main
    deb http://deb.debian.org/debian stretch-updates main
    ```

  - 然后去[清华大学镜像站](https://mirrors.tuna.tsinghua.edu.cn/)找到对应系统

    ![image-20210414202416372](Linux.assets/image-20210414202416372.png)

  - 记住`https`有可能会导致失败！酌情替换为`http`

  - 一种是直接全部替换，另一种是使用强大的文本替换命令（方便在Dockerfile执行）

    ```sh
    sed -i "s@http://deb.debian.org@http://mirrors.tuna.tsinghua.edu.cn@g" /etc/apt/sources.list
    sed -i "s@http://security.debian.org@http://mirrors.tuna.tsinghua.edu.cn@g" /etc/apt/sources.list
    ```

- 更新包

  - `apt-get clean`
  - `rm -rf /var/lib/apt/lists/*`
  - `rm -rf /etc/apt/source.list.d/*`
  - `apt-get update`

---

*2021.06.30*

### 在远程服务器上安装图形化界面

> [阿里云Ubuntu服务器图形界面配置（详细步骤，萌新看过来）_zephyr的博客-CSDN博客_阿里云ubuntu图形界面](https://blog.csdn.net/qq_43551263/article/details/88411955)
>
> [How to allow GUI root login on Ubuntu 20.04 Focal Fossa Linux - LinuxConfig.org](https://linuxconfig.org/how-to-allow-gui-root-login-on-ubuntu-20-04-focal-fossa-linux)

以Ubuntu20.04为例

- 安装

  ```sh
  apt-get update
  apt-get upgrade
  apt-get install ubuntu-desktop
  reboot
  ```

- 开启GUI的root登录权限

  - 确认拥有root密码，并以root用户登录终端

  - `vim /etc/gdm3/custom.conf`：在daemon下添加一行`AllowRoot=true`

    ```sh
    # GDM configuration storage
    # 
    # See /usr/share/gdm/gdm.schemas for a list of available options.
    
    [daemon]
    # Uncomment the line below to force the login screen to use Xorg
    #WaylandEnable=false
    
    # Enabling automatic login
    #  AutomaticLoginEnable = true
    #  AutomaticLogin = user1
    
    # Enabling timed login
    #  TimedLoginEnable = true
    #  TimedLogin = user1
    #  TimedLoginDelay = 10
    AllowRoot=true
    [security]
    
    [xdmcp]
    
    [chooser]
    
    [debug]
    # Uncomment the line below to turn on debugging
    # More verbose logs
    # Additionally lets the X server dump core if it crashes
    #Enable=true
    ```

  - `vim /etc/pam.d/gdm-password `：注释`auth  required        pam_succeed_if.so user != root quiet_success`

    ```sh
    #%PAM-1.0
    auth    requisite       pam_nologin.so
    # auth  required        pam_succeed_if.so user != root quiet_success
    @include common-auth
    auth    optional        pam_gnome_keyring.so
    @include common-account
    
    ...
    ```

  - `reboot`
  
  ---
  
  *2022.02.25*
  
  ### 查看系统信息
  
  - `uname -a`：显示电脑以及操作系统的相关信息。 