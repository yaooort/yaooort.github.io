---
title: Node.js安装的标准姿势
cover: 'http://prr6elh1d.sabkt.gdipper.com/static/images/IMG_3213.jpg'
date: 2019-05-19 23:01:59
subtitle: Node.js安装标准姿势
categories:
- js
tags:
- js
---
### 1.卸载node.js
- 为了对后续安装不造成影响，先卸载本地已经安装好的版本，如果是新的设备不需要，直接安装即可。推荐使用nvm管理多个版本的node.js。

mac 完全卸载node.js [使用这位老兄的办法，好使。](https://blog.csdn.net/shiquanqq/article/details/78032943)

##### homebrew安装的
* 直接一条命令 
```console
brew uninstall node
```
##### 官网下载pkg安装包的
* 一条命令 
```console
sudo rm -rf /usr/local/{bin/{node,npm},lib/node_modules/npm,lib/node,share/man/*/node.*}
```
##### 其他
* 编写脚本uninstallnode.sh

```sh
#!/bin/bash
lsbom -f -l -s -pf /var/db/receipts/org.nodejs.pkg.bom \
| while read i; do
  sudo rm /usr/local/${i}
done
sudo rm -rf /usr/local/lib/node \
     /usr/local/lib/node_modules \
     /var/db/receipts/org.nodejs.*
```

* 修改文件权限 chmod 777 uninstallNodejs.sh 
在命令行执行

### 2.安装[nvm](https://github.com/creationix/nvm)

* 推荐使用这种方式
```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```
* 或者
```
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```
* 安装完成，测试nvm 是否安装成功
```nvm
nvm --version
```
- 如果正常显示版本号，则说明安装成功，直接略过下面
* 这种安装方式会自动添加环境变量，但是最好先检查一下环境变量是否配置正确
```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```
* 重新加载系统环境变量
```
source ~/.bash_profile
```

##### 3.使用nvm管理node
* 查看远程node版本
```nvm
nvm ls-remote 
```
* 安装远程线上node，nvm install 版本号
```nvm
nvm install 8.11.3
```
* 查看本地已安装版本，可以看到当前使用的版本是v8.10.0
```
yaodeMacBook-Pro:/ yao$ nvm ls
->      v8.10.0
        v8.11.3
default -> 8.11.3 (-> v8.11.3)
node -> stable (-> v8.11.3) (default)
stable -> 8.11 (-> v8.11.3) (default)
iojs -> N/A (default)
lts/* -> lts/carbon (-> v8.11.3)
lts/argon -> v4.9.1 (-> N/A)
lts/boron -> v6.14.3 (-> N/A)
lts/carbon -> v8.11.3

```
* 使用一个版本
```
nvm use v8.11.3
```
##### 4.使用
* 直接命令行交互式
```
yaodeMacBook-Pro:/ yao$ node
> var a = 12
undefined
> var b = 22
undefined
> a+b
34
> 
```
* 使用node xxx.js
```
yaodeMacBook-Pro:node yao$ node serverhello.js 
Server running at http://127.0.0.1:8888/
```
* 安装nodemon 自动监听js改动，自动重新执行
```
npm install nodemon
```
##### 5.模块的使用
* Node.js的模块系统采用CommonJS规范
> CommonJS标准规定 
> *  一个单独的文件就是一个模块
> * 模块内将需要对外暴露的变量放到exports对象里，可以是任意对象，函数，数组等，未放到exports对象里的都是私有的。
> * 使用require方法加载模块，即读取模块文件获得exports对象
> * 默认每个.js文件都有一个module，可以使用module.exports暴露对外的模块方法
> * 使用require引入模块时，模块路径后面的js文件建议不加后缀（.js）
```javascript
//默认每个js文件模块为一个空对象
exports = module.exports = {}

//可以使用,这种方式给扩展添加对外的属性
exports.xxx = xxx;

//也可以使用这种方式直接赋值对象,更换原有的对象
module.exports = {xxx:xxx}

//不要使用，直接改变该对象地址，会导致module.exports对象不会有任何改变。
exports = {}
```

##### 6.全局变量和模块属性
###### 全局变量
* __dirname :当前模块所在目录路径
* __filename : 当前模块的文件名称
* console
 ###### 模块属性
* module.id : 模块的唯一标识
* module.loaded : 标记模块是否加载
* module.parent :  引用模块的模块

###### 模块分类
* 核心模块：node.js内置的模块
* 文件模块：自己写的模块和第三方提供的模块
