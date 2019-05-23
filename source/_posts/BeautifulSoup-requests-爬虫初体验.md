---
title: BeautifulSoup requests 爬虫初体验
cover: http://prr6elh1d.sabkt.gdipper.com/static/images/IMG_3241.jpg
date: 2019-05-22 15:50:39
subtitle: 爬虫BeautifulSoup使用
categories:
- 爬虫
tags:
- Python
- BeautifulSoup
---


### BeautifulSoup requests 爬虫初体验

------

说爬虫不得不提python

常用的Python爬虫库(摘自知乎[hunt zhan](https://www.zhihu.com/people/huntzhan))

> * Beautiful Soup。名气大，整合了一些常用爬虫需求。缺点：不能加载JS。
> * Scrapy。看起来很强大的爬虫框架，可以满足简单的页面爬取（比如可以明确获知url pattern的情况）。用这个框架可以轻松爬下来如亚马逊商品信息之类的数据。但是对于稍微复杂一点的页面，如weibo的页面信息，这个框架就满足不了需求了。
> * mechanize。优点：可以加载JS。缺点：文档严重缺失。不过通过官方的example以及人肉尝试的方法，还是勉强能用的。
> * selenium。这是一个调用浏览器的driver，通过这个库你可以直接调用浏览器完成某些操作，比如输入验证码。
> * cola。一个分布式爬虫框架。项目整体设计有点糟，模块间耦合度较高，不过值得借鉴。

#### Beautiful Soup 网页解析还是非常简单粗暴的(配合requests)基本的爬取没有任何问题

在这个数据称王的时代，没有数据对于内容性产品而言无疑是半条腿走路。(虽说这样做有些不要脸，但是没有办法，一些公开无版权的内容信息诱惑力太大，如果人工摘取工作量太复杂)

### 废话少说，劈柴

##### 1.安装库

- beautifulsoup4
- [官方中文文档](https://beautifulsoup.readthedocs.io/zh_CN/v4.4.0/)

```
pip install beautifulsoup4
```

- requests
- [官方中文文档](https://2.python-requests.org//zh_CN/latest/user/quickstart.html)

```
pip install requests
```

- 安装解析器

```
pip install lxml
```



| 解析器           | 使用方法                                                     | 优势                                                  | 劣势                                            |
| :--------------- | ------------------------------------------------------------ | ----------------------------------------------------- | ----------------------------------------------- |
| Python标准库     | `BeautifulSoup(markup, "html.parser")`                       | Python的内置标准库执行速度适中文档容错能力强          | Python 2.7.3 or 3.2.2)前 的版本中文档容错能力差 |
| lxml HTML 解析器 | `BeautifulSoup(markup, "lxml")`                              | 速度快文档容错能力强                                  | 需要安装C语言库                                 |
| lxml XML 解析器  | `BeautifulSoup(markup, ["lxml-xml"])``BeautifulSoup(markup, "xml")` | 速度快唯一支持XML的解析器                             | 需要安装C语言库                                 |
| html5lib         | `BeautifulSoup(markup, "html5lib")`                          | 最好的容错性以浏览器的方式解析文档生成HTML5格式的文档 | 速度慢不依赖外部扩展                            |




##### 2.具体步骤

```flow
st=>start: 使用requests请求到网页html
op1=>operation: 使用beautifulsoup4加载这个html
op2=>operation: 解析搜索网页可用元素
op3=>operation: 取出元素的内容
op4=>operation: 处理数据
e=>end: 存库

st->op1->op2->op3->op4->e
```

##### 3.常用操作

```python
# 加载网页对象，使用lxml解析库
soup = BeautifulSoup(html, 'lxml')

# 获取class为more的标签
bp = soup.find_all(attrs={"class": 'more'})

# 获取<main></main>标签
bp = soup.main

# 查找所有p标签
p = soup.find_all('p')

# 使用正则匹配(含有data-number属性并且值为数字的)
soup.find_all(attrs={"data-number": re.compile("^[0-9]*$")})

# 获取main标签下的所有子标签
as = soup.main.descendants

# 遍历子标签
for child in as:
    # 判断子标签是否为tag(因为只有tag才能继续查找,NavigableString只是一个字符串)
    if isinstance(child, Tag):
        # 如果标签是span标签并且含有id属性且id属性的值为counter且内容不为空
        if child.name == 'span' and 'id' in child.attrs and child['id'] == 'counter' and child.string:
            # 取出该标签下一个兄弟标签的第一个子标签的内容
            # .contents 和 .children
			# tag的 .contents 属性可以将tag的子节点以列表的方式输出:
        	child.next_sibling.contents[0].string
            
# 获取所有子标签的内容并过滤空内容
for string in soup.main.stripped_strings:
	print(string)

# 父标签
child.parent
# .next_sibling 和 .previous_sibling 分别是下一个兄弟标签和上一个兄弟标签

# 如果要去属性值可以直接使用
child['src']

```



熟悉以上操作就可以很方便的爬取html纯数据网页，你值得拥有！



