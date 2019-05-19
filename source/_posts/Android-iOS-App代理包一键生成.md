---
title: Android/iOS-App代理包一键生成
cover: http://prr6elh1d.sabkt.gdipper.com/static/images/IMG_3279.jpg
date: 2019-05-19 22:50:39
subtitle: 快速生成代理包
categories:
- Android
- iOS
tags:
- Python
- Android
- iOS
---
# APP快速打代理包工具


*前言：*
代理包可以快速帮助推广运营进行多渠道分发：  

 - 实现多渠道打包及iOS企业级分发包的快速生成渠道包
 - 只需要一个Android iOS原始包即可快速生成N个渠道商代理包
 - 支持生成之后快速扫二维码下载该渠道包测试
 - python单文件GUI开发。方便拆解及替换app内文件，及功能模块单独使用
 - 可编译为windows桌面程序和mac桌面程序

-------------------

## 上效果图 
![Jietu20180727-103944.jpg](http://prr6elh1d.sabkt.gdipper.com/static/images/2975942-78ee969e74ad85b6.jpg)

## 注意事项

1. Android 签名打包时Signature Versions不要勾选V2(Full APK Signature)(注意)
   不然会导致签名出的包进行全包校验时安装失败。（由于打包时会将文件写入Apk中，Apk文件sha256变化之后 签名文件认证不过通）
2. ios 可以使用企业签名包签出原始包进行打包
3. Android签过之后的包不能再签，需要改Python代码

-------------------
## 使用方式

### Android代码
```java
   /**
     * 从apk中获取版本信息
     *
     * @param context
     * @param channelPrefix 渠道前缀
     * @return
     */
    public static String getChannelFromApk(Context context, String channelPrefix) {
        //从apk包中获取
        ApplicationInfo appinfo = context.getApplicationInfo();
        String sourceDir = appinfo.sourceDir;
        //默认放在meta-inf/里， 所以需要再拼接一下
        String key = "META-INF/" + channelPrefix;
        String ret = "";
        ZipFile zipfile = null;
        try {
            zipfile = new ZipFile(sourceDir);
            Enumeration<?> entries = zipfile.entries();
            while (entries.hasMoreElements()) {
                ZipEntry entry = ((ZipEntry) entries.nextElement());
                String entryName = entry.getName();
                if (entryName.startsWith(key)) {
                    ret = entryName;
                    break;
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (zipfile != null) {
                try {
                    zipfile.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        String[] split = ret.split(channelPrefix);
        String channel = "";
        if (split != null && split.length >= 2) {
            channel = ret.substring(key.length());
        }
        StorageLW.getSingleton().put(StorageKEY.APP_CHANNEL, channel);
        return channel;
    }
```

### iOS使用方式
```objective-c
    /*! 读取渠道ID */
    + (NSString *)channel{
       NSString *resourceDirectory = [[NSBundle mainBundle] resourcePath];
       NSString *CodeSignaturePath = [resourceDirectory stringByAppendingPathComponent:@"/_CodeSignature/AppInfo.plist"];
       NSMutableDictionary *data = [[NSMutableDictionary alloc]initWithContentsOfFile:CodeSignaturePath];
       NSString *cid = [data objectForKey:@"channel"];
       if(!cid){
           cid = @"";
       }
       return cid;
    }
```

## 实现原理




- *Android apk的zip包内META-INF描述文件夹内写入空文件。文件名就是我们要打的渠道包ID


- *iOS 在ipa内选择Payload/{$APP_NAME}/_CodeSignaturre/ 下写入AppInfo.plist文件 然后读取该文件内的channel key


## 使用区域网服务器作为下载服务器，如果要使用iOS扫描下载安装。则需要修改本地python代码 搭建本地https环境 创建CA证书安装到iPhone上

## 使用Python 源码 需要安装依赖库





# [源码](https://github.com/yaooort/Apputils)

