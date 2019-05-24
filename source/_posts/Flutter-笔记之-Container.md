# Flutter 笔记之 Container

* #### 官方解释

> 一个拥有绘制、定位、调整大小的 widget。
>
> 可以确定的是这个玩意用的很广泛，它可以为一个View提供相对于父view位置的控制，及边框，边距，缩放，背景等属性

* #### 示例代码

```dart
body: Center(
            // Center控件后面再看
            child: Container(
          // 设置内部控件居中
          alignment: Alignment.center,
          // 背景色
          color: Colors.white30,
          // 只能有一个控件
          child: Container(
            // 宽高
            width: 300,
            height: 300,
            // 内部控件偏移 1.0 表示x方向最右边，y轴方向最下边
            alignment: Alignment(1.0, 1.0),
            // 设置背景边框，覆盖到内部控件之下
            decoration: BoxDecoration(
              color: Colors.red,
              border:
                  Border.all(color: Color(0xFFFF0000), width: 0.5), // 边色与边宽度
              borderRadius: new BorderRadius.circular((60.0)), // 圆角度
              // borderRadius: BorderRadius.vertical(top: Radius.elliptical(20, 50)),// 也可控件一边圆角大小
              // // 生成俩层阴影，一层绿，一层黄， 阴影位置由offset决定,阴影模糊层度由blurRadius大小决定（大就更透明更扩散），阴影模糊大小由spreadRadius决定
              boxShadow: [
                BoxShadow(
                    color: Colors.amber,
                    offset: Offset(5.0, 5.0),
                    blurRadius: 10.0,
                    spreadRadius: 2.0),
                BoxShadow(color: Colors.yellowAccent, offset: Offset(1.0, 1.0)),
                BoxShadow(color: Colors.greenAccent)
              ],
            ),
            // 覆盖到内部控件之上
            foregroundDecoration: BoxDecoration(
              border: new Border.all(
                  color: Color(0xFFFFFF00), width: 0.5), // 边色与边宽度
              color: Color(0xFF9E9E9E), // 底色
              //        shape: BoxShape.circle, // 圆形，使用圆形时不可以使用borderRadius
              shape: BoxShape.circle, // 默认值也是矩形
              // borderRadius: new BorderRadius.circular((20.0)), // 圆角度
            ),
            // 约束内部控件及自身的大小
            constraints: BoxConstraints(
              minWidth: 0,
              minHeight: 0,
              maxHeight: double.infinity,
              maxWidth: double.infinity
            ),
            // 外边距
            margin: EdgeInsets.all(30),
            // 内边距
            // padding: EdgeInsets.all(10),
            // 变换z轴缩放
            transform: new Matrix4.rotationZ(0.2),
            padding: EdgeInsets.fromLTRB(10, 10, 10, 10),
            child: Container(
              color: Colors.blueAccent,
              width: 100,
              height: 100,
            ),
          ),
        )),
```

* 注意点

alignment设置了之后，就会依照父控件的constraints:BoxConstraints 确定宽高。这时候你再去设置宽高就不起作用了

BoxConstraints 主要有四个属性：

> minWidth
>
> maxWidth
>
> minHeight
>
> maxHeight



* #### Decoration 背景[摘自](https://blog.csdn.net/chenlove1/article/details/83627831)

1 继续关系：


BoxDecoration:实现边框、圆角、阴影、形状、渐变、背景图像

ShapeDecoration:实现四个边分别指定颜色和宽度、底部线、矩形边色、圆形边色、体育场（竖向椭圆）、 角形（八边角）边色

FlutterLogoDecoration:实现Flutter图片

UnderlineTabindicator:下划线

2 介绍
一个背景装饰对象，相当于Android中的shape.xml，定制各种各样的背景（边框、圆角、阴影、形状、渐变、背景图像）。

3 BoxDecoration例子：
构造方法：

  const BoxDecoration({
    this.color, // 底色
    this.image, // 图片
    this.border, 边色
    this.borderRadius, // 圆角度
    this.boxShadow, // 阴影
    this.gradient, // 渐变
    this.backgroundBlendMode, // 混合Mode
    this.shape = BoxShape.rectangle,  // 形状
  }) 
3.1 边框+圆角:
decoration: new BoxDecoration(
  border: new Border.all(color: Color(0xFFFF0000), width: 0.5), // 边色与边宽度
  color: Color(0xFF9E9E9E), // 底色
  //        borderRadius: new BorderRadius.circular((20.0)), // 圆角度
  borderRadius: new BorderRadius.vertical(top: Radius.elliptical(20, 50)), // 也可控件一边圆角大小
),
3.2 阴影：
decoration: new BoxDecoration(
    border: new Border.all(color: Color(0xFFFF0000), width: 0.5), // 边色与边宽度
// 生成俩层阴影，一层绿，一层黄， 阴影位置由offset决定,阴影模糊层度由blurRadius大小决定（大就更透明更扩散），阴影模糊大小由spreadRadius决定
    boxShadow: [BoxShadow(color: Color(0x99FFFF00), offset: Offset(5.0, 5.0),    blurRadius: 10.0, spreadRadius: 2.0), BoxShadow(color: Color(0x9900FF00), offset: Offset(1.0, 1.0)), BoxShadow(color: Color(0xFF0000FF))],
),
3.3 形状（圆形与矩形）：
decoration: new BoxDecoration(
  border: new Border.all(color: Color(0xFFFFFF00), width: 0.5), // 边色与边宽度
  color: Color(0xFF9E9E9E), // 底色
  //        shape: BoxShape.circle, // 圆形，使用圆形时不可以使用borderRadius
  shape: BoxShape.rectangle, // 默认值也是矩形
  borderRadius: new BorderRadius.circular((20.0)), // 圆角度
),
3.4 渐变（环形、扫描式、线性）：
decoration: new BoxDecoration(
  border: new Border.all(color: Color(0xFFFFFF00), width: 0.5), // 边色与边宽度
// 环形渲染
  gradient: RadialGradient(colors: [Color(0xFFFFFF00), Color(0xFF00FF00), Color(0xFF00FFFF)],radius: 1, tileMode: TileMode.mirror)
//扫描式渐变
//        gradient: SweepGradient(colors: [Color(0xFFFFFF00), Color(0xFF00FF00), Color(0xFF00FFFF)], startAngle: 0.0, endAngle: 1*3.14)
// 线性渐变
//        gradient: LinearGradient(colors: [Color(0xFFFFFF00), Color(0xFF00FF00), Color(0xFF00FFFF)], begin: FractionalOffset(1, 0), end: FractionalOffset(0, 1))
),
3.4 背景图像：
decoration: new BoxDecoration(
  border: new Border.all(color: Color(0xFFFFFF00), width: 0.5), // 边色与边宽度
  image: new DecorationImage(
  image: new NetworkImage('https://avatar.csdn.net/8/9/A/3_chenlove1.jpg'), // 网络图片
  // image: new AssetImage('graphics/background.png'), 本地图片
  fit: BoxFit.fill // 填满
  //          centerSlice: new Rect.fromLTRB(270.0, 180.0, 1360.0, 730.0),// 固定大小
  ),
),
4 ShapeDecoration例子：
构造方法：

const ShapeDecoration({
  this.color,
  this.image,
  this.gradient,
  this.shadows,
  @required this.shape,
})
除了shape，其他与BoxDecoration一致，shape研究：    

decoration: new ShapeDecoration(
  color: Color(0xFFFF00FF), // 底色
  // 统一四边颜色和宽度
  shape: Border.all(color: Color(0xFF00FFFF),style: BorderStyle.solid,width: 2)
// 四个边分别指定颜色和宽度， 当只给bottom时与UnderlineInputBorder一致效果
//          shape: Border(top: b, bottom: b, right: b, left: b)
// 底部线
//          shape: UnderlineInputBorder( borderSide:BorderSide(color: Color(0xFFFFFFFF), style: BorderStyle.solid, width: 2))
// 矩形边色
//        shape: RoundedRectangleBorder(borderRadius: BorderRadius.all(Radius.circular(10)), side: BorderSide(color: Color(0xFFFFFFFF), style: BorderStyle.solid, width: 2))
// 圆形边色
//        shape: CircleBorder(side: BorderSide(color: Color(0xFFFFFF00), style: BorderStyle.solid, width: 2))
// 体育场（竖向椭圆）
//        shape: StadiumBorder(side: BorderSide(width: 2, style: BorderStyle.solid, color: Color(0xFF00FFFF))
// 角形（八边角）边色
//          shape: BeveledRectangleBorder(borderRadius: BorderRadius.all(Radius.circular(10)), side: BorderSide(color: Color(0xFFFFFFFF), style: BorderStyle.solid, width: 2))
),
5 FlutterLogoDecoration例子：
构造方法：

const FlutterLogoDecoration({
  this.lightColor = const Color(0xFF42A5F5), // Colors.blue[400]
  this.darkColor = const Color(0xFF0D47A1), // Colors.blue[900]
  this.textColor = const Color(0xFF616161),
  this.style = FlutterLogoStyle.markOnly,
  this.margin = EdgeInsets.zero,
}) 
这个就不解析了，Flutter的logo，开发没啥用！

6 UnderlineTabindicator例子：
构造方法：

const UnderlineTabIndicator({
  this.borderSide = const BorderSide(width: 2.0, color: Colors.white),
  this.insets = EdgeInsets.zero,
})
这个比较简单就是加下划线，可以设置Insets值（控制下划高底，左右边距）

decoration: new UnderlineTabIndicator(
  borderSide: BorderSide(width: 2.0, color: Colors.white),
  insets: EdgeInsets.fromLTRB(0,0,0,10)
),



