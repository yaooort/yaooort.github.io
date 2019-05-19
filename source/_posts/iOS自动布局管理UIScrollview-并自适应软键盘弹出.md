---
title: 'iOS自动布局管理UIScrollview,并自适应软键盘弹出'
cover: 'http://prr6elh1d.sabkt.gdipper.com/static/images/IMG_3820.jpg'
date: 2019-05-19 22:58:01
subtitle: iOS自动布局处理键盘弹出及UIScrollview
categories:
- iOS
tags:
- iOS
- 自动布局
---
### iOS 自动布局管理scrollview

- 个人整理笔记，包含完美软键盘弹出视图





```objective-c
#define MAS_SHORTHAND
#define MAS_SHORTHAND_GLOBALS
#define RGBColor(r, g, b) [UIColor colorWithRed:(r)/255.0 green:(g)/255.0 blue:(b)/255.0 alpha:1]

#define RGBAColor(r, g, b ,a) [UIColor colorWithRed:(r)/255.0 green:(g)/255.0 blue:(b)/255.0 alpha:a]

#define RandColor RGBColor(arc4random_uniform(255), arc4random_uniform(255), arc4random_uniform(255))

#import "ViewController.h"
#import "Masonry.h"

@interface ViewController ()<UITextFieldDelegate,UIScrollViewDelegate>

@property(nonatomic,strong) UIScrollView *mainScroll;

@property(nonatomic,strong) UITextField *textField;

@property(nonatomic,strong) UIView *countView;

//@property(nonatomic,assign) BOOL keyBoardlsVisible;

@property(nonatomic,assign) CGFloat insetBottom;
@end

@implementation ViewController



- (void)viewDidLoad {
    [super viewDidLoad];
    //  给 _keyBoardlsVisible 赋初值
    //    _keyBoardlsVisible = NO;
    self.insetBottom = 0;
    
    self.mainScroll = [UIScrollView new];
    [self.mainScroll setBackgroundColor:[UIColor whiteColor]];
    self.mainScroll.delegate = self;
    [self.view addSubview:self.mainScroll];
    [self.mainScroll makeConstraints:^(MASConstraintMaker *make) {
        make.edges.equalTo(self.view);
    }];
    
    
#pragma mark--包含-个View的布局方式
    //    self.countView = [[UIView alloc] init];
    //    self.countView.backgroundColor = [UIColor greenColor];
    //    [self.mainScroll addSubview:self.countView];
    //
    //    [self.countView makeConstraints:^(MASConstraintMaker *make) {
    //这句其实是指定view填充contentSize
    //        make.edges.equalTo(self.mainScroll);
    //确定宽度等于滚动条宽度，也是确定contentSize宽度的关键
    //        make.width.equalTo(self.mainScroll);
    //    }];
    //由于上面没有指定contentSize高度，此时无法确定滚动高度
    //
    //    UIImageView *imageView1 = [UIImageView new];
    //    [imageView1 setBackgroundColor:RandColor];
    //    [self.countView addSubview:imageView1];
    //    [imageView1 makeConstraints:^(MASConstraintMaker *make) {
    //        make.top.equalTo(self.countView).offset(20);
    //        make.left.right.mas_equalTo(self.countView);
    //        make.height.mas_equalTo(900);
    //    }];
    //
    //    UIImageView *imageView2 = [UIImageView new];
    //    [imageView2 setBackgroundColor:RandColor];
    //    [self.countView addSubview:imageView2];
    //    [imageView2 makeConstraints:^(MASConstraintMaker *make) {
    //        make.top.equalTo(imageView1.bottom).offset(20);
    //        make.left.right.mas_equalTo(self.countView);
    //        make.height.mas_equalTo(900);
    //    }];
    //
    //    //当视图高度大于屏幕的时候scrollView就可以滚动，设置宽度的方法相同
    //    [self.countView updateConstraints:^(MASConstraintMaker *make) {
    //        // 设置容器视图的底部与要显示的子控件的底部相同，不然scrollView不能滚动
    //        make.bottom.equalTo(imageView2);
    //    }];
    
#pragma mark--包含多个View的布局方式
    for (int i=0; i<40; i++) {
        UITextField *textF = [UITextField new];
        textF.delegate = self;//设置代理
        textF.backgroundColor = RandColor;
        [self.mainScroll addSubview:textF];
        [textF makeConstraints:^(MASConstraintMaker *make) {
            //确定宽度等于滚动条宽度，也是确定contentSize宽度的关键
            make.width.equalTo(self.mainScroll);
            //左边对齐
            make.left.equalTo(self.mainScroll);
            //y轴计算
            make.top.equalTo(self.mainScroll).offset(i*45);
            //高度写死
            make.height.equalTo(40);
        }];
        if(i==39){
            //到最后一个View时候更新scollerView的contentSize高度也就是底部最多延伸到哪里
            [self.mainScroll updateConstraints:^(MASConstraintMaker *make) {
                make.bottom.equalTo(textF.bottom);
            }];
        }
    }
    
    
    
    // 注册键盘通知
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(keyboardWillChangeFrameNotification:) name:UIKeyboardWillChangeFrameNotification object:nil];
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(keyboardWillHideNotification:) name:UIKeyboardWillHideNotification object:nil];
}

#pragma mark--代理方法键盘弹出
-(void)textFieldDidBeginEditing:(UITextField *)textField
{   //得到当前操作的view
    self.textField = textField;
}

#pragma mark--代理方法键盘关闭
-(void)textFieldDidEndEditing:(UITextField *)textField
{   //得到当前操作的view
    self.textField = textField;
}

#pragma mark--键盘弹出
- (void)keyboardWillChangeFrameNotification:(NSNotification *)notification {
    
    // 获取键盘基本信息（动画时长与键盘高度）
    NSDictionary *userInfo = [notification userInfo];
    CGRect rect = [userInfo[UIKeyboardFrameEndUserInfoKey] CGRectValue];
    CGFloat keyboardHeight   = CGRectGetHeight(rect);
    CGFloat keyboardDuration = [userInfo[UIKeyboardAnimationDurationUserInfoKey] doubleValue];
    //获得当前输入框的Y坐标
    CGFloat inputY = self.textField.frame.origin.y+self.textField.frame.size.height;
    //滑动距离
    CGFloat offsetY = self.mainScroll.contentOffset.y;
    //输入框距离屏幕底部
    CGFloat inputJd = self.mainScroll.frame.size.height-(inputY-offsetY);
    
    NSLog(@"键盘高度==%f,输入框距离底部距离==%f",keyboardHeight,inputJd);
    if(inputJd<keyboardHeight){
        self.insetBottom = keyboardHeight-inputJd;
        // 更新滚动条
        [UIView animateWithDuration:keyboardDuration animations:^{
            [self.mainScroll setContentOffset:CGPointMake(0, offsetY+self.insetBottom) animated:YES];
        }];
    }
}
#pragma mark--键盘隐藏
- (void)keyboardWillHideNotification:(NSNotification *)notification {
    
    // 获得键盘动画时长
    NSDictionary *userInfo   = [notification userInfo];
    CGFloat keyboardDuration = [userInfo[UIKeyboardAnimationDurationUserInfoKey] doubleValue];
    //滑动距离
    CGFloat offsetY = self.mainScroll.contentOffset.y;
    // 更新滚动条
    [UIView animateWithDuration:keyboardDuration animations:^{
        [self.mainScroll setContentOffset:CGPointMake(0, offsetY-self.insetBottom) animated:YES];
    }];
    self.insetBottom = 0;
    
}
#pragma mark--触摸事件
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
    [super touchesBegan:touches withEvent:event];
    [self.view endEditing:YES];
}

- (void)scrollViewWillBeginDragging:(UIScrollView *)scrollView{
    [self.view endEditing:YES];
}
#pragma mark--移除通知
- (void)dealloc {
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}

@end


```
