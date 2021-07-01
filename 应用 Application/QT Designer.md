# QT Designer

*summary*

在linux系统上可以用**QT Designer**开发桌面应用程序

---

*pinned*

### Ubuntu配置QT开发环境

> [Python 使用QT5开发界面的一个demo开发过程的总结 - 阿布先生 - 博客园 (cnblogs.com)](https://www.cnblogs.com/bu123/p/9895105.html)
>
> [ubuntu下安装qtDesigner，用来进行python界面设计_lightningqw的专栏-CSDN博客](https://blog.csdn.net/lightningqw/article/details/79113335)

#### 下载Python QT包

- `pip install pyqt5`

- 测试安装是否成功

  ```python
  from PyQt5 import QtWidgets, QtGui
  import sys
  
  app = QtWidgets.QApplication(sys.argv)
  window = QtWidgets.QWidget();
  window.show()
  sys.exit(app.exec_())
  ```

#### 安装QT Designer

- `sudo apt-get install qt5-default qttools5-dev-tools`
- 终端执行`designer`即可
