## 什么是XPath？

xpath（XML Path Language）是一门在XML和HTML文档中查找信息的语言，可用来在XML和HTML文档中对元素和属性进行遍历。

## XPath开发工具

1. Chrome插件XPath Helper。
2. Firefox插件Try XPath。

## XPath语法

### 选取节点：

XPath 使用路径表达式来选取 XML 文档中的节点或者节点集。这些路径表达式和我们在常规的电脑文件系统中看到的表达式非常相似。

| 表达式   | 描述                                                         | 示例           | 结果                            |
| -------- | ------------------------------------------------------------ | -------------- | ------------------------------- |
| nodename | 选取此节点的所有子节点                                       | bookstore      | 选取bookstore下所有的子节点     |
| /        | 如果是在最前面，代表从根节点选取。否则选择某节点下的某个节点 | /bookstore     | 选取根元素下所有的bookstore节点 |
| //       | 从全局节点中选择节点，随便在哪个位置                         | //book         | 从全局节点中找到所有的book节点  |
| @        | 选取某个节点的属性                                           | //book[@price] | 选择所有拥有price属性的book节点 |
| .        | 当前节点                                                     | ./a            | 选取当前节点下的a标签           |

### 谓语：

谓语用来查找某个特定的节点或者包含某个指定的值的节点，被嵌在方括号中。
在下面的表格中，我们列出了带有谓语的一些路径表达式，以及表达式的结果：

| 路径表达式                   | 描述                                  |
| ---------------------------- | ------------------------------------- |
| /bookstore/book[1]           | 选取bookstore下的第一个子元素         |
| /bookstore/book[last()]      | 选取bookstore下的倒数第二个book元素。 |
| bookstore/book[position()<3] | 选取bookstore下前面两个子元素。       |
| //book[@price]               | 选取拥有price属性的book元素           |
| //book[@price=10]            | 选取所有属性price等于10的book元素     |

### 通配符

*表示通配符。

| 通配符 | 描述                 | 示例         | 结果                          |
| ------ | -------------------- | ------------ | ----------------------------- |
| *      | 匹配任意节点         | /bookstore/* | 选取bookstore下的所有子元素。 |
| @*     | 匹配节点中的任何属性 | //book[@*]   | 选取所有带有属性的book元素。  |

### 选取多个路径：

通过在路径表达式中使用“|”运算符，可以选取若干个路径。
示例如下：

```
//bookstore/book | //book/title
# 选取所有book元素以及book元素下所有的title元素
```

### 运算符：

![img](assets/QQ图片20171124194846.png)

## lxml库

lxml 是 一个HTML/XML的解析器，主要的功能是如何解析和提取 HTML/XML 数据。

lxml和正则一样，也是用 C 实现的，是一款高性能的 Python HTML/XML 解析器，我们可以利用之前学习的XPath语法，来快速的定位特定元素以及节点信息。

lxml python 官方文档：<http://lxml.de/index.html>

需要安装C语言库，可使用 pip 安装：pip install lxml

### 基本使用：

我们可以利用他来解析HTML代码，并且在解析HTML代码的时候，如果HTML代码不规范，他会自动的进行补全。示例代码如下：

```python
# 使用 lxml 的 etree 库
from lxml import etree 

text = '''
<div>
    <ul>
         <li class="item-0"><a href="link1.html">first item</a></li>
         <li class="item-1"><a href="link2.html">second item</a></li>
         <li class="item-inactive"><a href="link3.html">third item</a></li>
         <li class="item-1"><a href="link4.html">fourth item</a></li>
         <li class="item-0"><a href="link5.html">fifth item</a> # 注意，此处缺少一个 </li> 闭合标签
     </ul>
 </div>
'''

#利用etree.HTML，将字符串解析为HTML文档
html = etree.HTML(text) 

# 按字符串序列化HTML文档
result = etree.tostring(html) 

print(result)
```

输入结果如下：

```html
<html><body>
<div>
    <ul>
         <li class="item-0"><a href="link1.html">first item</a></li>
         <li class="item-1"><a href="link2.html">second item</a></li>
         <li class="item-inactive"><a href="link3.html">third item</a></li>
         <li class="item-1"><a href="link4.html">fourth item</a></li>
         <li class="item-0"><a href="link5.html">fifth item</a></li>
</ul>
 </div>
</body></html>
```

可以看到。lxml会自动修改HTML代码。例子中不仅补全了li标签，还添加了body，html标签。

### 从文件中读取html代码：

除了直接使用字符串进行解析，lxml还支持从文件中读取内容。我们新建一个hello.html文件：

```html
<!-- hello.html -->
<div>
    <ul>
         <li class="item-0"><a href="link1.html">first item</a></li>
         <li class="item-1"><a href="link2.html">second item</a></li>
         <li class="item-inactive"><a href="link3.html"><span class="bold">third item</span></a></li>
         <li class="item-1"><a href="link4.html">fourth item</a></li>
         <li class="item-0"><a href="link5.html">fifth item</a></li>
     </ul>
 </div>
```

然后利用`etree.parse()`方法来读取文件。示例代码如下：

```python
from lxml import etree

# 读取外部文件 hello.html
html = etree.parse('hello.html')
result = etree.tostring(html, pretty_print=True)

print(result)
```

输入结果和之前是相同的。

### 在lxml中使用XPath语法：

1. 获取所有li标签：

   ```python
    from lxml import etree
   
    html = etree.parse('hello.html')
    print type(html)  # 显示etree.parse() 返回类型
   
    result = html.xpath('//li')
   
    print(result)  # 打印<li>标签的元素集合
   ```

2. 获取所有li元素下的所有class属性的值：

   ```python
    from lxml import etree
   
    html = etree.parse('hello.html')
    result = html.xpath('//li/@class')
   
    print(result)
   ```

3. 获取li标签下href为`www.baidu.com`的a标签：

   ```python
    from lxml import etree
   
    html = etree.parse('hello.html')
    result = html.xpath('//li/a[@href="www.baidu.com"]')
   
    print(result)
   ```

4. 获取li标签下所有span标签：

   ```python
    from lxml import etree
   
    html = etree.parse('hello.html')
   
    #result = html.xpath('//li/span')
    #注意这么写是不对的：
    #因为 / 是用来获取子元素的，而 <span> 并不是 <li> 的子元素，所以，要用双斜杠
   
    result = html.xpath('//li//span')
   
    print(result)
   ```

5. 获取li标签下的a标签里的所有class：

   ```python
    from lxml import etree
   
    html = etree.parse('hello.html')
    result = html.xpath('//li/a//@class')
   
    print(result)
   ```

6. 获取最后一个li的a的href属性对应的值：

   ```python
    from lxml import etree
   
    html = etree.parse('hello.html')
   
    result = html.xpath('//li[last()]/a/@href')
    # 谓语 [last()] 可以找到最后一个元素
   
    print(result)
   ```

7. 获取倒数第二个li元素的内容：

   ```python
    from lxml import etree
   
    html = etree.parse('hello.html')
    result = html.xpath('//li[last()-1]/a')
   
    # text 方法可以获取元素内容
    print(result[0].text)
   ```

8. 获取倒数第二个li元素的内容的第二种方式：

   ```python
    from lxml import etree
   
    html = etree.parse('hello.html')
    result = html.xpath('//li[last()-1]/a/text()')
   
    print(result)
   ```

## 使用requests和xpath爬取电影天堂

示例代码如下：

```python
import requests
from lxml import etree

BASE_DOMAIN = 'http://www.dytt8.net'
HEADERS = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/61.0.3163.100 Safari/537.36',
    'Referer': 'http://www.dytt8.net/html/gndy/dyzz/list_23_2.html'
}

def spider():
    url = 'http://www.dytt8.net/html/gndy/dyzz/list_23_1.html'
    resp = requests.get(url,headers=HEADERS)
    # resp.content：经过编码后的字符串
    # resp.text：没有经过编码，也就是unicode字符串
    # text：相当于是网页中的源代码了
    text = resp.content.decode('gbk')
    # tree：经过lxml解析后的一个对象，以后使用这个对象的xpath方法，就可以
    # 提取一些想要的数据了
    tree = etree.HTML(text)
    # xpath/beautifulsou4
    all_a = tree.xpath("//div[@class='co_content8']//a")
    for a in all_a:
        title = a.xpath("text()")[0]
        href = a.xpath("@href")[0]
        if href.startswith('/'):
            detail_url = BASE_DOMAIN + href
            crawl_detail(detail_url)
            break

def crawl_detail(url):
    resp = requests.get(url,headers=HEADERS)
    text = resp.content.decode('gbk')
    tree = etree.HTML(text)
    create_time = tree.xpath("//div[@class='co_content8']/ul/text()")[0].strip()
    imgs = tree.xpath("//div[@id='Zoom']//img/@src")
    # 电影海报
    cover = imgs[0]
    # 电影截图
    screenshoot = imgs[1]
    # 获取span标签下所有的文本
    infos = tree.xpath("//div[@id='Zoom']//text()")
    for index,info in enumerate(infos):
        if info.startswith("◎年　　代"):
            year = info.replace("◎年　　代","").strip()

        if info.startswith("◎豆瓣评分"):
            douban_rating = info.replace("◎豆瓣评分",'').strip()
            print(douban_rating)

        if info.startswith("◎主　　演"):
            # 从当前位置，一直往下面遍历
            actors = [info]
            for x in range(index+1,len(infos)):
                actor = infos[x]
                if actor.startswith("◎"):
                    break
                actors.append(actor.strip())
            print(",".join(actors))


if __name__ == '__main__':
    spider()
```

## chrome相关问题：

在62版本（目前最新）中有一个bug，在页面302重定向的时候不能记录FormData数据。这个是这个版本的一个bug。详细见以下链接：<https://stackoverflow.com/questions/34015735/http-post-payload-not-visible-in-chrome-debugger。>

在金丝雀版本中已经解决了这个问题，可以下载这个版本继续，链接如下：<https://www.google.com/chrome/browser/canary.html>

## 作业：

使用requests和xpath爬取腾讯招聘网信息。要求为获取每个职位的详情信息。