
vscode中运行多线程代码 https://blog.csdn.net/Hao_jiu/article/details/114930919

# 基本设置

## 还原默认设置

How to reset settings in Visual Studio Code? https://stackoverflow.com/questions/36108515/how-to-reset-settings-in-visual-studio-code
- https://stackoverflow.com/questions/36108515/how-to-reset-settings-in-visual-studio-code/36109176#36109176
  * > For a more manual route, the settings files are located in the following locations:
    + > Windows `%APPDATA%\Code\User\settings.json`
    + > macOS `$HOME/Library/Application Support/Code/User/settings.json`
    + > Linux `$HOME/.config/Code/User/settings.json`
  * >> //notes：起因就是垃圾 gitpod 的自动用本地 vscode 连接远程 gitpod workspace 功能，春节期间随手一用还挺好，然后收假回来发现悲剧了：vsc 连不上单位的虚机了。。。后来发现是 gitpod 篡改了默认使用的 ssh config 文件（原来默认是用 `～/.ssh/config`，它给改到指向它自己的 `/var/folders/xx/xxxxxxxxxxx` 下面的某个 ssh 配置文件了），于是修改回来，但是还是连不上。
    >>> 然后看了下连接过程中 vsc 打印的日志，发现有个什么 `.sock` 也是在用 gitpod 目录下的。我次噢，这个不知道怎么改。。。于是网上搜了搜恢复 vsc 的默认设置。按这个回答里这一部分，直接删了 Mac 那个路径下的 `settings.json`，重启 vsc，终于远程连接成功。。。

## 过滤 `.pyc`、`node_modules/` 等文件或路径，使其不在 vsc 的 Explorer 中显示

How to exclude folder from "Explore" tab? https://stackoverflow.com/questions/33258543/how-to-exclude-folder-from-explore-tab
- https://stackoverflow.com/questions/33258543/how-to-exclude-folder-from-explore-tab/55556206#55556206

# 首次安装后配置

Windows下VSCode配置C++环境 - 小红花的文章 - 知乎 https://zhuanlan.zhihu.com/p/105135431
