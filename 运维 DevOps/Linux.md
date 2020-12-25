# Linux

*summary*

<img src="..\template.assets\image-20201223131536639.png" alt="image-20201223131536639" style="zoom:22%;" />

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