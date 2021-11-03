### 安装系统
硬件：一个引导系统安装的启动u盘、一台win系统电脑、一块移动硬盘

软件：ubuntu20.04系统（可以从清华镜像站下载）

详细步骤参考：https://blog.csdn.net/fanxueya1322/article/details/90205143

500G硬盘的ubuntu系统分区建议：
![](https://files.mdnice.com/user/20439/c39e8c00-8634-4204-aaae-7ecdc5b7b7a7.png)

开机后按热键进入bios更改启动项，即可进入linux系统

### 迁移R包
如果需要将一台设备安装的R包，在另外一台设备上安装，
首先保存A设备上的R包名字列表，在另外一台设备上写一个循环进行安装。

#在A设备上保存名字列表
oldip <- installed.packages()[,1]
save(oldip,file = "installedPacckages.Rdata")

#在B设备上进行安装；
load("installedPacckages.Rdata")
newip <- installed.packages()[,1]
 for (i in setdiff(oldip,newip)) {
  install.packages(i)
}

参考：http://blog.sciencenet.cn/blog-3448646-1275987.html

### 为rstudio指定R版本

参考：https://docs.rstudio.com/ide/server-pro/r-versions.html

如果您在备用位置安装了 R 版本，您可以在``/etc/rstudio/r-versions``配置文件中列出它们。例如：

```
/opt/R-3.2.1
/opt/R-devel-3.3
```

### 在ubuntu上安装常用软件

apt常用指令：

```
sudo apt install 软件名   #安装软件

sudo apt remove 软件名   #卸载软件

sudo apt update   #  更新可用软件包列表，确认一下哪些软件可升级

sudo apt upgrade    #  更新已安装的包
```
安装deb压缩包的软件:
```
sudo dpkg -i 安装包.deb
```

参考：https://blog.csdn.net/qq_39236499/article/details/108983709

### 调节声音
打开屏幕阅读器再关上以后，多媒体播放声音变得非常奇怪

输入`sudo alsamixer`之后按左右键瞎调两下变好了。

方法参考：https://blog.csdn.net/marwenx/article/details/78962457
