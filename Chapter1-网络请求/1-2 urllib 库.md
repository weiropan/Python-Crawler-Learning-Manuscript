`urllib`库是`Python`中一个最基本的网络请求库。可以模拟浏览器的行为，向指定的服务器发送一个请求，并可以保存服务器返回的数据。

### urlopen函数：

在`Python3`的`urllib`库中，所有和网络请求相关的方法，都被集到`urllib.request`模块下面了，以先来看下`urlopen`函数基本的使用：

```python
# -*- coding: utf-8 -*-
"""
Created on Thu Oct 11 21:57:44 2018
@author: weiro
"""

from urllib import request
url = 'https://www.sogou.com/'
resp = request.urlopen(url)
print(resp.read())

print(type(resp)) # 返回值是一个http.client.HTTPResponse对象
```

以上三行代码的作用是打印出搜狗首页的全部代码。使用浏览器访问搜狗首页时，右键查看源代码，会发现跟我们刚才打印出来的数据是一模一样的。

以下对`urlopen`函数的进行详细讲解：

1. 默认情况下 request.urlopen(url) 处理的是 get 请求, request.urlopen(url,data) 处理的是 post 请求。 
2. url`：请求的url。
3. `data`：请求的`data`，如果设置了这个值，那么将变成`post`请求。
4. 返回值：返回值是一个`http.client.HTTPResponse`对象，这个对象是一个**类文件句柄对象**。有`read(size)`、`readline`、`readlines`以及`getcode`（获取当前响应状态码）等方法。

### urlretrieve函数：

这个函数可以方便的将网页上的一个文件保存到本地。可以迅捷的下载你想要爬取的图片、网页、视屏等。

以下代码可以非常方便的将一张图片下载到本地：

```python
from urllib import request
url = 'https://img04.sogoucdn.com/app/a/100520024/77b176fe026f1813790b0e8a176c2e3b'
filename = 'beautiful.png'
request.urlretrieve(url,filename)
```

### 参数编码与解码函数

#### urlencode函数：

用浏览器发送请求的时候，如果url中包含了**中文或者其他特殊字符**，那么浏览器会自动进行编码。而如果使用代码发送请求，那么就必须手动的进行编码，这时候就应该使用`urlencode`函数来实现。`urlencode`可以把字典数据转换为`URL`编码的数据。示例代码如下：

```python
from urllib import parse
data = {'name':'爬虫基础','greet':'hello world','age':100}
qs = parse.urlencode(data)
print(qs)
```

#### parse_qs函数：

可以将经过编码后的url参数进行解码。示例代码如下：

```python
from urllib import parse
qs = "name=%E7%88%AC%E8%99%AB%E5%9F%BA%E7%A1%80&greet=hello+world&age=100"
print(parse.parse_qs(qs))
```

### urlparse和urlsplit：

有时候拿到一个url，想要对这个url中的各个组成部分进行分割，那么这时候就可以使用`urlparse`或者是`urlsplit`来进行分割。示例代码如下：

```python
from urllib import request,parse

url = 'http://www.baidu.com/s?username=zhiliao'

result = parse.urlsplit(url)
# result = parse.urlparse(url)

print('scheme:',result.scheme)
print('netloc:',result.netloc)
print('path:',result.path)
print('query:',result.query)
```

`urlparse`和`urlsplit`基本上是一模一样的。唯一不一样的地方是，`urlparse`里面多了一个`params`属性，而`urlsplit`没有这个`params`属性。比如有一个`url`为：`url = 'http://www.baidu.com/s;hello?wd=python&username=abc#1'`，
那么`urlparse`可以获取到`hello`，而`urlsplit`不可以获取到。`url`中的`params`也用得比较少。

### request.Request类：

如果想要在请求的时候增加一些请求头，那么就必须使用`request.Request`类来实现。比如要增加一个`User-Agent`，示例代码如下：

```python
from urllib import request

headers = {
'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36'
}
req = request.Request("http://www.baidu.com/",headers=headers)
resp = request.urlopen(req)
print(resp.read())
```

### 内涵段子爬虫实战作业：

1. url链接：<http://neihanshequ.com/bar/1/>
2. 要求：能爬取一页的数据就可以了。

### ProxyHandler处理器（代理设置）

很多网站会检测某一段时间某个IP的访问次数(通过流量统计，系统日志等)，如果访问次数多的不像正常人，它会禁止这个IP的访问。
所以我们可以设置一些代理服务器，每隔一段时间换一个代理，就算IP被禁止，依然可以换个IP继续爬取。
urllib中通过ProxyHandler来设置使用代理服务器，下面代码说明如何使用自定义opener来使用代理：

```python
from urllib import request

# 这个是没有使用代理的
# resp = request.urlopen('http://httpbin.org/get')
# print(resp.read().decode("utf-8"))

# 这个是使用了代理的
handler = request.ProxyHandler({"http":"218.66.161.88:31769"})

opener = request.build_opener(handler)
req = request.Request("http://httpbin.org/ip")
resp = opener.open(req)
print(resp.read())
```

常用的代理有：

- 西刺免费代理IP：<http://www.xicidaili.com/>
- 快代理：<http://www.kuaidaili.com/>
- 代理云：<http://www.dailiyun.com/>

### 什么是cookie：

在网站中，http请求是无状态的。也就是说即使第一次和服务器连接后并且登录成功后，第二次请求服务器依然不能知道当前请求是哪个用户。`cookie`的出现就是为了解决这个问题，第一次登录后服务器返回一些数据（cookie）给浏览器，然后浏览器保存在本地，当该用户发送第二次请求的时候，就会自动的把上次请求存储的`cookie`数据自动的携带给服务器，服务器通过浏览器携带的数据就能判断当前用户是哪个了。`cookie`存储的数据量有限，不同的浏览器有不同的存储大小，但一般不超过4KB。因此使用`cookie`只能存储一些小量的数据。

#### cookie的格式：

```
Set-Cookie: NAME=VALUE；Expires/Max-age=DATE；Path=PATH；Domain=DOMAIN_NAME；SECURE
```

参数意义：

- NAME：cookie的名字。
- VALUE：cookie的值。
- Expires：cookie的过期时间。
- Path：cookie作用的路径。
- Domain：cookie作用的域名。
- SECURE：是否只在https协议下起作用。

### 使用cookielib库和HTTPCookieProcessor模拟登录：

Cookie 是指网站服务器为了辨别用户身份和进行Session跟踪，而储存在用户浏览器上的文本文件，Cookie可以保持登录信息到用户下次与服务器的会话。
这里以人人网为例。人人网中，要访问某个人的主页，必须先登录才能访问，登录说白了就是要有cookie信息。那么如果我们想要用代码的方式访问，就必须要有正确的cookie信息才能访问。解决方案有两种，第一种是使用浏览器访问，然后将cookie信息复制下来，放到headers中。示例代码如下：

```python
from urllib import request

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36',
    'Cookie': 'anonymid=jacdwz2x-8bjldx; depovince=GW; _r01_=1; _ga=GA1.2.1455063316.1511436360; _gid=GA1.2.862627163.1511436360; wp=1; JSESSIONID=abczwY8ecd4xz8RJcyP-v; jebecookies=d4497791-9d41-4269-9e2b-3858d4989785|||||; ick_login=884e75d4-f361-4cff-94bb-81fe6c42b220; _de=EA5778F44555C091303554EBBEB4676C696BF75400CE19CC; p=61a3c7d0d4b2d1e991095353f83fa2141; first_login_flag=1; ln_uact=970138074@qq.com; ln_hurl=http://hdn.xnimg.cn/photos/hdn121/20170428/1700/main_nhiB_aebd0000854a1986.jpg; t=3dd84a3117737e819dd2c32f1cdb91d01; societyguester=3dd84a3117737e819dd2c32f1cdb91d01; id=443362311; xnsid=169efdc0; loginfrom=syshome; ch_id=10016; jebe_key=9c062f5a-4335-4a91-bf7a-970f8b86a64e%7Ca022c303305d1b2ab6b5089643e4b5de%7C1511449232839%7C1; wp_fold=0'
}

url = 'http://www.renren.com/880151247/profile'

req = request.Request(url,headers=headers)
resp = request.urlopen(req)
with open('renren.html','w') as fp:
    fp.write(resp.read().decode('utf-8'))
```

但是每次在访问需要cookie的页面都要从浏览器中复制cookie比较麻烦。在Python处理Cookie，一般是通过`http.cookiejar`模块和`urllib模块的HTTPCookieProcessor`处理器类一起使用。`http.cookiejar`模块主要作用是提供用于存储cookie的对象。而`HTTPCookieProcessor`处理器主要作用是处理这些cookie对象，并构建handler对象。

#### http.cookiejar模块：

该模块主要的类有CookieJar、FileCookieJar、MozillaCookieJar、LWPCookieJar。这四个类的作用分别如下：

1. CookieJar：管理HTTP cookie值、存储HTTP请求生成的cookie、向传出的HTTP请求添加cookie的对象。整个cookie都存储在内存中，对CookieJar实例进行垃圾回收后cookie也将丢失。
2. FileCookieJar (filename,delayload=None,policy=None)：从CookieJar派生而来，用来创建FileCookieJar实例，检索cookie信息并将cookie存储到文件中。filename是存储cookie的文件名。delayload为True时支持延迟访问访问文件，即只有在需要时才读取文件或在文件中存储数据。
3. MozillaCookieJar (filename,delayload=None,policy=None)：从FileCookieJar派生而来，创建与Mozilla浏览器 cookies.txt兼容的FileCookieJar实例。
4. LWPCookieJar (filename,delayload=None,policy=None)：从FileCookieJar派生而来，创建与libwww-perl标准的 Set-Cookie3 文件格式兼容的FileCookieJar实例。

#### 登录人人网：

利用`http.cookiejar`和`request.HTTPCookieProcessor`登录人人网。相关示例代码如下：

```python
from urllib import request,parse
from http.cookiejar import CookieJar

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36'
}

def get_opener():
    cookiejar = CookieJar()
    handler = request.HTTPCookieProcessor(cookiejar)
    opener = request.build_opener(handler)
    return opener

def login_renren(opener):
    data = {"email": "970138074@qq.com", "password": "pythonspider"}
    data = parse.urlencode(data).encode('utf-8')
    login_url = "http://www.renren.com/PLogin.do"
    req = request.Request(login_url, headers=headers, data=data)
    opener.open(req)

def visit_profile(opener):
    url = 'http://www.renren.com/880151247/profile'
    req = request.Request(url,headers=headers)
    resp = opener.open(req)
    with open('renren.html','w') as fp:
        fp.write(resp.read().decode("utf-8"))

if __name__ == '__main__':
    opener = get_opener()
    login_renren(opener)
    visit_profile(opener)
```

#### 保存cookie到本地：

保存`cookie`到本地，可以使用`cookiejar`的`save`方法，并且需要指定一个文件名：

```python
from urllib import request
from http.cookiejar import MozillaCookieJar

cookiejar = MozillaCookieJar("cookie.txt")
handler = request.HTTPCookieProcessor(cookiejar)
opener = request.build_opener(handler)

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36'
}
req = request.Request('http://httpbin.org/cookies',headers=headers)

resp = opener.open(req)
print(resp.read())
cookiejar.save(ignore_discard=True,ignore_expires=True)
```

#### 从本地加载cookie：

从本地加载`cookie`，需要使用`cookiejar`的`load`方法，并且也需要指定方法：

```python
from urllib import request
from http.cookiejar import MozillaCookieJar

cookiejar = MozillaCookieJar("cookie.txt")
cookiejar.load(ignore_expires=True,ignore_discard=True)
handler = request.HTTPCookieProcessor(cookiejar)
opener = request.build_opener(handler)

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36'
}
req = request.Request('http://httpbin.org/cookies',headers=headers)

resp = opener.open(req)
print(resp.read())
```