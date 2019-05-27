---
title: Flutter-之Row/Column
categories: 
- Flutter
tags: 
- Android
- iOS
cover: 'http://prr6elh1d.sabkt.gdipper.com/static/images/IMG_4171.jpg'
date: 2019-05-27 15:25:44
subtitle: Flutter-之Row/Column
---


---

### 1.Row/Column

> 这是横向排列（Row）和纵向排列（Column）的布局控件



##### 1.1两者用法基本一样

> 可以适应两种场景
>
> 一种是等分宽或高，使用Expanded自由控制每个子控件的比例

```dart
Expanded(
        flex: 1, //比例
        child: Container(
          height: 100,
          color: Colors.blueAccent,
          child: Card(
            color: Colors.brown,
            child: Center(
              child: Text('你好'),
            ),
          ),
        ),
      );
```

> 另一种是使用控件固定大小
>
> 例如，默认设置下，这三个控件在屏幕中央排成一排并且，内容以居中形式呈现

```dart
children: <Widget>[
              Text('这是一个文字'),
              Container(
                width: 100,
                height: 100,
                color: Colors.blueGrey,
                child: Icon(IconData(0xe900, fontFamily: "CustomIcons")),
              ),
              Card(
                child: Image.asset("assets/logo.png"),
              )
            ],
```

##### 1.2基本属性设置

> **MainAxisAlignment**：主轴方向上的对齐方式，会对child的位置起作用，默认是start。
>
> 其中MainAxisAlignment枚举值：
>
> - center：将children放置在主轴的中心；
> - end：将children放置在主轴的末尾；
> - spaceAround：将主轴方向上的空白区域均分，使得children之间的空白区域相等，但是首尾child的空白区域为1/2；
> - spaceBetween：将主轴方向上的空白区域均分，使得children之间的空白区域相等，首尾child都靠近首尾，没有间隙；
> - spaceEvenly：将主轴方向上的空白区域均分，使得children之间的空白区域相等，包括首尾child；
> - start：将children放置在主轴的起点；
>
> 其中spaceAround、spaceBetween以及spaceEvenly的区别，就是对待首尾child的方式。其距离首尾的距离分别是空白区域的1/2、0、1。

> ***总结：MainAxisAlignment 可以控制子控件在row/column中的间隙及排列，例如等分屏幕宽度或者高度，然后将子控件之间的间距进行调整*** 注意使用SingleChildScrollView包裹row/column该属性失效，具体失效原因应该是冲突

> **MainAxisSize**：在主轴方向占有空间的值，默认是max。
>
> MainAxisSize的取值有两种：
>
> - max：根据传入的布局约束条件，最大化主轴方向的可用空间；
> - min：与max相反，是最小化主轴方向的可用空间；

> CrossAxisAlignment枚举值有如下几种：
>
> - baseline：在交叉轴方向，使得children的baseline对齐；
> - center：children在交叉轴上居中展示；
> - end：children在交叉轴上末尾展示；
> - start：children在交叉轴上起点处展示；
> - stretch：让children填满交叉轴方向；

>
>
>**VerticalDirection**：定义了children摆放顺序，默认是down。
>
>VerticalDirection枚举值有两种：
>
>- down：从top到bottom进行布局；
>- up：从bottom到top进行布局。
>
>top对应Row以及Column的话，就是左边和顶部，bottom的话，则是右边和底部。



##### 2.如果需要超出屏幕进行滑动，只需要使用SingleChildScrollView包裹Row/Column就可以

```dart
Scaffold(
      body: SafeArea(
        child: SizedBox.expand(
            child: SingleChildScrollView(
          scrollDirection: Axis.horizontal,
          child: Row(
            mainAxisAlignment: MainAxisAlignment.start,
            mainAxisSize: MainAxisSize.min,
            crossAxisAlignment: CrossAxisAlignment.start,
            verticalDirection: VerticalDirection.down,
            children: <Widget>[
              Text('这是一个文字'),
              Container(
                width: 100,
                height: 100,
                color: Colors.blueGrey,
                child: Icon(IconData(0xe900, fontFamily: "CustomIcons")),
              ),
              Card(
                child: Image.asset("assets/logo.png"),
              ),
              Card(
                child: Image.asset("assets/logo.png"),
              ),Card(
                child: Image.asset("assets/logo.png"),
              ),Card(
                child: Image.asset("assets/logo.png"),
              ),Card(
                child: Image.asset("assets/logo.png"),
              ),Container(
                width: 100,
                height: 100,
                color: Colors.blueGrey,
                child: Icon(IconData(0xe900, fontFamily: "CustomIcons")),
              ),
            ],
          ),
        )),
      ),
```



##### 2.1 SafeArea确定控件的安全区域

> 刘海屏的顶部及底部安全区域均可单独设置

```dart
const SafeArea({
    Key key,
    this.left = true,
    this.top = true,
    this.right = true,
    this.bottom = true,
    this.minimum = EdgeInsets.zero,
    @required this.child,
  }) : assert(left != null),
       assert(top != null),
       assert(right != null),
       assert(bottom != null),
       super(key: key);

```

##### 2.2 SingleChildScrollView 的包裹view的大小超出可以滑动，默认没有滚动条

```dart
const SingleChildScrollView({
    Key key,
    this.scrollDirection = Axis.vertical,
    this.reverse = false,
    this.padding,
    bool primary,
    this.physics,
    this.controller,
    this.child,
    this.dragStartBehavior = DragStartBehavior.start,
  }) : assert(scrollDirection != null),
       assert(dragStartBehavior != null),
       assert(!(controller != null && primary == true),
          'Primary ScrollViews obtain their ScrollController via inheritance from a PrimaryScrollController widget. '
          'You cannot both set primary to true and pass an explicit controller.'
       ),
       primary = primary ?? controller == null && identical(scrollDirection, Axis.vertical),
       super(key: key);

```



参考文章

[Flutter 布局（七）- Row、Column详解](https://juejin.im/post/5b623d8c5188257f0b583c77)

[Flutter之Row/Column用法详解](https://juejin.im/post/5c6a5892e51d4542331c5528)

