---
title: Flutter之Image
categories:
- Flutter
tags:
- Android
- iOS
cover: 'http://prr6elh1d.sabkt.gdipper.com/static/images/'
date: 2019-05-28 10:34:38
subtitle: Flutter之Image使用
---

### 1.介绍

---

Flutter 中的Image 加载图片的控件，包含网络图，资源图，本地图等常用的加载方法

* 构造

```dart
const Image({
    Key key,
    @required this.image,
    this.semanticLabel,
    this.excludeFromSemantics = false,
    this.width,
    this.height,
    this.color,
    this.colorBlendMode,
    this.fit,
    this.alignment = Alignment.center,
    this.repeat = ImageRepeat.noRepeat,
    this.centerSlice,
    this.matchTextDirection = false,
    this.gaplessPlayback = false,
    this.filterQuality = FilterQuality.low,
  }) : assert(image != null),
       assert(alignment != null),
       assert(repeat != null),
       assert(filterQuality != null),
       assert(matchTextDirection != null),
       super(key: key);
```

* 使用RawImage实现图片的渲染,RawImage继承自LeafRenderObjectWidget，使用RenderImage将像素信息通过paintImage画到canvas。

```dart
@override
  Widget build(BuildContext context) {
    final RawImage image = RawImage(
      image: _imageInfo?.image,
      width: widget.width,
      height: widget.height,
      scale: _imageInfo?.scale ?? 1.0,
      color: widget.color,
      colorBlendMode: widget.colorBlendMode,
      fit: widget.fit,
      alignment: widget.alignment,
      repeat: widget.repeat,
      centerSlice: widget.centerSlice,
      matchTextDirection: widget.matchTextDirection,
      invertColors: _invertColors,
      filterQuality: widget.filterQuality,
    );
    if (widget.excludeFromSemantics)
      return image;
    return Semantics(
      container: widget.semanticLabel != null,
      image: true,
      label: widget.semanticLabel == null ? '' : widget.semanticLabel,
      child: image,
    );
  }

```



### 2.常用方法

---

| 方法   | 解释 |
| ------ | ----- |
| Image() | 构造方法，使用ImageProvider实现 |
| Image.asset | 加载资源图片 |
| Image.file | 加载本地图片文件 |
| Image.network | 加载网络图片 |
| Image.memory | 加载Uint8List资源图片 |

* 构造详解

| 参数                 | 备注                       | 解释及常用方式                                           |
| -------------------- | -------------------------- | -------------------------------------------------------- |
| image                | @required                  | 传入ImageProvider<T>的实现，可参考FileImage的load方法    |
| semanticLabel        |                            | 官方解释是，图片的文字解释，是用于盲人使用的时候读出来。 |
| excludeFromSemantics | 控制semanticLabel          | 是否从语义中排除此图像。                                 |
| width，height        |                            | 图片控件的宽/高，与图片本身无关，只是屏幕显示区域        |
| color                | 混入颜色                   | 图片色值进行统一替换，与colorBlendMode一起使用更好       |
| colorBlendMode       | 混入模式                   | BlendMode枚举，样式太多，主要的有去除所有颜色            |
| fit                  |                            | 图片拉伸模式                                             |
| alignment            | 图片显示位置               | 内部图片显示的相对位置                                   |
| repeat               | 空白区域的显示             | 默认留空，可选一张接一张铺                               |
| centerSlice          | 类似于Android.9图          | 拉伸点，及内容区的控制                                   |
| matchTextDirection   | 必须使用Directionality包裹 | 用来显示图片的绘制方向，或者说是实现镜面效果             |
| gaplessPlayback      | 当图片提供地址改变         | 是否响应图片变化                                         |
| filterQuality        | 图像过滤器的质量级别。     | 质量越低加载速度越快，质量越高加载速度越慢               |

* fit图片拉伸模式

| 模式             | 解释                             |
| ---------------- | -------------------------------- |
| BoxFit.fill      | 全图显示，显示可能拉伸，充满     |
| BoxFit.contain   | 全图显示，显示原比例，不需充满   |
| BoxFit.cover     | 显示可能拉伸，可能裁剪，充满     |
| BoxFit.fitWidth  | 显示可能拉伸，可能裁剪，宽度充满 |
| BoxFit.fitHeight | 显示可能拉伸，可能裁剪，高度充满 |

* colorBlendMode

| 模式    | 解释                                                         |
| ------- | ------------------------------------------------------------ |
| Clear   | 删除源图像和目标图像，不留任何内容                           |
| Src     | 删除目标图像，仅绘制源图像                                   |
| Dst     | 源图像被丢弃                                                 |
| srcOver | 将源图像合成到目标图像上                                     |
| dstOver | 这与[srcOver]相反，合成目标图像下的源图像                    |
| srcIn   | 显示源图像，但仅限于两个图像重叠的位置。该目标图像未呈现，它被直接视为蒙版。该目的地的颜色通道被忽略，只有不透明度有效果 |
| dstIn   | 显示目标图像，但仅限于两个图像重叠的位置。该源图像未呈现，它仅被视为蒙版。颜色忽略源的通道，只有不透明度有效。 |

