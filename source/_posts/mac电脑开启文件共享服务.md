---
title: mac电脑开启文件共享服务
date: 2019-05-19 22:38:10
subtitle: MacOS 快速分享本地文件到局域网
categories:
- 劈柴
tags:
- Python2.7
- macOS
cover: http://prr6elh1d.sabkt.gdipper.com/static/images/IMG_3235.jpg
---
使用mac自带的Python 2.7命令快速共享本地文件.操作步骤
- 1.打开电脑终端
  ![屏幕快照 2019-03-21 上午11.45.20.png](http://prr6elh1d.sabkt.gdipper.com/static/images/mac电脑文件共享/2975942-6460fb75db8ad0d6.png)

- 2.进入你想共享的文件夹目录
![屏幕快照 2019-03-21 上午11.47.52.png](http://prr6elh1d.sabkt.gdipper.com/static/images/mac电脑文件共享/2975942-785157544aa83b4f.png)
```
 输入 cd /Users/用户/Desktop/ 进入桌面文件夹，共享桌面文件 
```

- 3.输入命令
```
python -m SimpleHTTPServer
```
![屏幕快照 2019-03-21 下午12.30.27.png](http://prr6elh1d.sabkt.gdipper.com/static/images/mac电脑文件共享/2975942-eb5707509e383e29.png)
> 表示在本机的8000端口号开启服务

- 4.在同一个局域网下的任意浏览器输入ip+端口进行访问
> 本机IP 按住option 点击wifi图标即可查看IP地址
![屏幕快照 2019-03-21 下午12.34.24.png](http://prr6elh1d.sabkt.gdipper.com/static/images/mac电脑文件共享/2975942-c090d92f3af55725.png)

- 5.在另外一台电脑打开该地址
```
http://10.88.10.141:8000
```
