title: '[使用mac自带的Python 2.7命令快速共享本地文件]'
date: 2019-05-19 00:00:00
---
------
### 使用mac自带的Python 2.7命令快速共享本地文件

.操作步骤

- 1.打开电脑终端
  ![屏幕快照 2019-03-21 上午11.45.20.png](https://upload-images.jianshu.io/upload_images/2975942-6460fb75db8ad0d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 2.进入你想共享的文件夹目录
![屏幕快照 2019-03-21 上午11.47.52.png](https://upload-images.jianshu.io/upload_images/2975942-785157544aa83b4f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
``` 输入 cd /Users/用户/Desktop/ 进入桌面文件夹，共享桌面文件 ```



- 3.输入命令
```python -m SimpleHTTPServer```
![屏幕快照 2019-03-21 下午12.30.27.png](https://upload-images.jianshu.io/upload_images/2975942-eb5707509e383e29.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> 表示在本机的8000端口号开启服务

- 4.在同一个局域网下的任意浏览器输入ip+端口进行访问
> 本机IP 按住option 点击wifi图标即可查看IP地址
![屏幕快照 2019-03-21 下午12.34.24.png](https://upload-images.jianshu.io/upload_images/2975942-c090d92f3af55725.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 5.在另外一台电脑打开该地址
```http://10.88.10.141:8000```
