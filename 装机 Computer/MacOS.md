# MacOS

*summary*

> [jaywcjlove/awesome-mac:  Now we have become very big, Different from the original idea. Collect premium software in various categories.](https://github.com/jaywcjlove/awesome-mac)

- MacOS的Typora复制URL时并不会携带网站标题，可以通过在Chrome浏览器安装TabCopy插件来解决。

  - > [TabCopy – 快速复制 Chrome 所有标签页的标题和地址并多格式输出[Chrome 扩展]](http://www.apprcn.com/tabcopy.html)

---

*pinned*

### 快捷键

> [Mac小技巧：教你如何在Mac电脑打出command?、option?等特殊符号 | 码农家园](https://www.codenong.com/cs110921108/)
>
> [Chrome keyboard shortcuts - Computer - Google Chrome Help](https://support.google.com/chrome/answer/157179?hl=en&co=GENIE.Platform%3DDesktop#zippy=%2Ctab-window-shortcuts)

- Fn：打开表情符号面板
- ⌘+Q：关闭应用
- ⌘+⌥+方向键右：Chrome切换标签页
- ⌘+空格：聚焦搜索
- Fn+方向键左：Home
- Fn+方向键右：End
- Fn+⌫：Delete
- ⌃+⌘+Q：锁屏
- 

---

*2022.07.12*

### 触控板手势

- 四指缩小/放大：应用台/应用/桌面
- 四指向上：调出后台
- 两指平移：

- - 浏览器：前进/后退

- 三指拖移：将三个手指同时放在触控板上，然后移动，即可拖动某个窗口，或者文件。
  - 这个手势功能需要手动开启：打开系统偏好设置辅助功能指针控制触控板选项启用拖移三指拖移（超好用）

- 三指轻点：鼠标定位在文字上，三个手指同时轻轻点击触控板，可查找或者翻译。

- 两指从触控板右侧滑入：打开通知中心

### 键盘文本短语

- **系统偏好设置 > 键盘 > 文本 > 添加输入码**
- 这样打出输入码后，就会显示对应短语

---

*2022.07.16*

### iTerm2+Oh My Zsh终端主题配置

> [iTerm2 + Oh My Zsh，打造专属你的终端神器 - 掘金](https://juejin.cn/post/6995169462399795208)

#### 快速安装（PowerLevel10k主题）

```sh
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k


vim ~/.zshrc

# 修改成下列文本
ZSH_THEME="powerlevel10k/powerlevel10k"
# 上述修改结束

source ~/.zshrc

# 安装 MesloLGS NF 字体后：
iTerm2->Preferences->Profiles->Text->MesloLGS NF

# 不断执行这个命令直到完全满意
p10k configure
```

#### 主题市场

[Themes · ohmyzsh/ohmyzsh Wiki · GitHub](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes)

#### 常见问题

##### 字体文件无法通过命令行下载

[GitHub - romkatv/powerlevel10k: A Zsh theme](https://github.com/romkatv/powerlevel10k#manual-font-installation)

- 或许是权限，或许是加速问题，总之离线安装可行

##### VS Code中终端字体设置

- VS Code 打开设置

- Terminal > Integrated > Font family > MesloLGS NF

##### sh脚本运行异常

- 以后可以用`/bin/bash`代替

---

*2022.07.17*

### Brew安装

> [The Missing Package Manager for macOS (or Linux) — Homebrew](https://brew.sh/)

- 如果安装超时：[homebrew | 镜像站使用帮助 | 清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirrors.tuna.tsinghua.edu.cn/help/homebrew/)
  - 「替换现有仓库上游」也是要做的
