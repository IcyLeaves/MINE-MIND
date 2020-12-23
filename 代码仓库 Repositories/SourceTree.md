# SourceTree

*summary*

<img src="SourceTree.assets\image-20201223130936952.png" alt="image-20201223130936952" style="zoom:10%;" />

**SourceTree**是一个统一管理代码仓库的可视化Git工具。由ATLASSIAN出品。

- 免费

---

*2020.12.23*

- [下载链接](https://www.sourcetreeapp.com/)

### 如何在SourceTree中使用SSH认证向GitHub推送代码？

- 首先，在本机上生成SSH公钥和私钥。

  - **SourceTree > 工具 > 创建或导入SSH密钥**

  - 请检查`C:/Users/xxx/.ssh`是否已经存在SSH密钥，如果存在请点击**Load Private Key**

  - 点击**Generate**，生成密钥对，其中最顶部以`ssh-rsa`开头的多行文本即为认证程序所需要的公钥（public key）

    <img src="SourceTree.assets\image-20201223141459686.png" alt="image-20201223141459686" style="zoom: 80%;" />

  - 然后点击**Save public key**(.pub)和**Save private key**(.ppk)，一并放到`C:/Users/xxx/.ssh`文件夹下（如果没有就创建）

- 然后查看系统托盘中名叫`Pageant(PuTTY authentication agent)`的后台程序，载入私钥。

  - **右键 > View Keys > Add Key**
  - 选择刚刚保存的private key载入

- 现在要将公钥上传到需要认证的GitHub账户。

  ![image-20201223143054655](SourceTree.assets\image-20201223143054655.png)

  - 登录[GitHub](https://github.com/settings/keys)，**Settings > SSH and GPG Keys > New SSH key**
  - 将刚刚的多行文本粘贴进去并保存

- 最后一步，设置远端并采用SSH认证方式。

  - 只需将远端地址从`http://...`的形式改为`git@github.com....`的形式即可，具体可在`Git Clone`处选择合适的URL

    <img src="SourceTree.assets\image-20201223143642613.png" alt="image-20201223143642613" style="zoom: 80%;" />

