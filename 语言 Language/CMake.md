# CMake

*summary*

![img](CMake.assets/cmake_logo_slider.png)



---

*pinned*

> [How to upgrade cmake in Ubuntu - Ask Ubuntu](https://askubuntu.com/questions/829310/how-to-upgrade-cmake-in-ubuntu)

### 升级CMake

- `sudo apt remove cmake`
- 访问[Download | CMake](https://cmake.org/download/)
  - 找到Linux Installer中的sh脚本，比如[cmake-3.21.3-linux-x86_64.sh](https://github.com/Kitware/CMake/releases/download/v3.21.3/cmake-3.21.3-linux-x86_64.sh)

- 拷贝到`/opt/`：在该目录下，`wget https://github.com/Kitware/CMake/releases/download/v3.21.3/cmake-3.21.3-linux-x86_64.sh`
- 使脚本可执行：`chmod +x /opt/cmake-3.${yourversion}.sh`

- `sudo bash /opt/cmake-3.${yourversion}.sh`
- `sudo ln -s /opt/cmake-3.${yourversion}/bin/* /usr/local/bin`

---

*2021.12.02*

### CentOS配置GCC

> [Linux gcc版本如何升级 | 《Linux就该这么学》 (linuxprobe.com)](https://www.linuxprobe.com/linux-gcc-upgrade.html)
>
> [linux - gcc 4.8.1 will not make - Stack Overflow](https://stackoverflow.com/questions/18242322/gcc-4-8-1-will-not-make)

- 从官方FTP([Index of /gnu/gcc](http://ftp.gnu.org/gnu/gcc/))获取对应版本的安装包：

```sh
wget http://ftp.gnu.org/gnu/gcc/gcc-9.3.0/gcc-9.3.0.tar.gz
tar -zxvf gcc-9.3.0.tar.gz
```

- 编译安装

```sh
cd gcc-9.3.0
./contrib/download_prerequisites
mkdir gcc-build-9.3.0
cd gcc-build-9.3.0
../configure -enable-checking=release -enable-languages=c,c++ -disable-multilib
make -j4
sudo make install
```

- 如果出现错误（如：configure: error: C++ compiler missing or inoperational），大概率因为没安装g++，

```c
yum install gcc-c++  
```

### CentOS配置GDB

> [linux下gdb的安装及查看方法_chengsi101的博客-CSDN博客_gdb安装](https://blog.csdn.net/chengsi101/article/details/79424083)
>
> [Linux-GLIBCXX版本过低导致编译错误--version `GLIBCXX_3.4.20' not found_qingtingmeng的专栏-CSDN博客](https://blog.csdn.net/qingtingmeng/article/details/49863841)
>
> [centos7安装gmp库_gusijin的博客-CSDN博客](https://blog.csdn.net/gusijin/article/details/116458393)

- 从官方FTP([Index of /gnu/gdb](https://ftp.gnu.org/gnu/gdb/))获取安装包

```sh
wget https://ftp.gnu.org/gnu/gdb/gdb-11.1.tar.gz
tar -zxvf gdb-11.1.tar.gz
```

- 编译安装

```sh
cd gdb-11.1
./configure
make -j4
make install
```

- GMP库安装（可能报错）

```sh
wget https://gmplib.org/download/gmp/gmp-6.2.1.tar.lz
lzip -dv gmp-6.2.1.tar.lz
tar -xvf gmp-6.2.1.tar
cd gmp-6.2.1
./configure
make -j4
make check
make install
```

- GCC软连接错误

```sh
sudo  find / -name  libstdc++.so.6*
ls -al /usr/lib64/libstdc++.so.6
cp /usr/local/lib64/libstdc++.so.6.0.20 /usr/lib64
sudo rm -rf /usr/lib64/libstdc++.so.6
ln -s /usr/lib64/libstdc++.so.6.0.20 /usr/lib64/libstdc++.so.6
```

