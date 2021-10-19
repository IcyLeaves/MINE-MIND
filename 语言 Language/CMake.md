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
- 使脚本可执行：`chmod +x /opt/cmake-3.*your_version*.sh`

- `sudo bash /opt/cmake-3.*your_version*.sh`
- `sudo ln -s /opt/cmake-3.*your_version*/bin/* /usr/local/bin`

