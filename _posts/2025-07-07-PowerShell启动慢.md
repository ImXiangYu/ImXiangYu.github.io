仅作个人记录。

### 太长不看：

安装 Anaconda（Miniconda）导致的 PowerShell 启动慢，解决方法如下：

1. 启动 Powershell，输入`$PROFILE`，显示目录。
2. 进入对应目录，有 `profile.ps1` 文件，打开。
3. 注释掉所有行，问题解决。
4. 后续通过 Anaconda PowerShell Prompt 使用conda

### 详细

最近在搞大模型，因此装了 miniconda，装完之后 PowerShell 每次启动都会显示

**加载个人及系统配置文件用了 xxx 毫秒。**

![img](https://picx.zhimg.com/v2-2173c1f0737bbf65b70aa93280c7566b_1440w.jpg)

启动慢

平时都是秒启动，现在开一次 PowerShell 要好几秒，实在是有点难以接受。问了一下 AI，给出的回答是使用指令：

```powershell
conda config --set auto_activate_base false
```

说是可以跳过基础环境的自动激活，大幅减少初始化时间。

![img](https://pic2.zhimg.com/v2-8e143d16733ff2295f99ced13ec37945_1440w.jpg)

实测发现差别不大

但实测发现好像没啥变化，可能是我装的包少。

后续就想到对配置文件进行修改，于是上网查，发现了解决办法。

1. 启动 Powershell，输入`$PROFILE`，显示目录。

![img](https://pic1.zhimg.com/v2-87ebd049f70cce44d98a2a825e2d8450_1440w.jpg)

码掉的是自己的用户名

2. 进入对应目录，发现有个以 ps1 结尾的文件，要改的就是这个 ps 脚本

![img](https://pic2.zhimg.com/v2-81c34f06bf6f8fb3dd13249fdef1d1bb_1440w.jpg)

ps1结尾的文件

3. 打开文件后，对所有行进行注释，取消掉 ps 脚本的作用。

![img](https://pic3.zhimg.com/v2-f20cb8e7259fe4d3e793ca52a7995c66_1440w.jpg)

注释掉全部代码

注释之后再次打开 PowerShell，秒启动~，问题解决。

后续使用 conda 可以直接使用安装自带的 Anaconda PowerShell Prompt