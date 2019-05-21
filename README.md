# Python Spider-1-Urllib

>Urllib 是 Python 自带的标准库，其通常用于爬虫开发、API（应用程序编程接口）数据获取和测试。

在 Python3 中，Urllib 是一个收集几个模块来使用 URL 的软件包，大致具备以下功能。
- **urllib.request** : 用于打开和读取 URL。
- **urllib.error** : 包含提出的例外 urllib.request。
- **urllib.parse** : 用于解析 URL。
- **urllib.robotparser** : 用于解析 robot.txt 文件 。

### 1. 发送请求

**urllib.request.urlopen** 的语法如下：
urllib.request.urlopen(url, data = None, [timeout, ]*, cafile = None, capath = None, cadefault = False, context = None).

**参数解释：**
- **url：** 需要访问的网站的URL地址。url 格式必须完整，如 https://movie.douban.com 为完整的 url，若 url 为 movie.douban.com/，则程序运行时会提示无法识别 url 的错误。
- **data：** 默认值为 None，Urllib 判断参数 data 是否为 None 从而区分请求方式。若参数 data 为 None, 则代表请求方式为 GET; 反之请求方式为POST，发送 POST 请求。参数 data 以字典形式存储数据，并将参数 data 由字典类型转换成字节类型才能完成 POST 请求。
- **timeout：** 超时设置，指定阻塞操作(请求时间)的超时(如果未指定，就使用全局默认超时设置)。
- **cafile**、**capath** 和 **cadefault：** 使用参数指定一组 HTTPS 请求的可信CA证书。caflie 应指向包含一组 CA 证书的单个文件；capath 应指向证书文件的目录；cadefault 通常使用默认值即可。
- **conlext：** 描述各种 SSL 选项的实例。


当对网站发送请求时，网站会返回相应的响应内容。urlopen 对象提供获取网站相应内容的方法函数，分别介绍如下。
- **read()**、**readline()**、**readines()**、**fileno()** 和 **close()：**对 HTTPResponse 类型数据操作。
- **info()：**返回 HTTPMessage 对象，表示远程服务器返回的头信息。
- **getcode()：**返回 HTTP 状态码。
- **geturl()：**返回请求的 URL。

**示例：**
```
# 导入 urllib
import urllib.request
# 打开 URL
responce = urllib.request.urlopen('https://movie.douban.com/', None, 2)
# 读取返回内容
html = responce.read().decode('utf-8')
# 写入 txt
with open('html.txt', 'w', encoding = 'utf-8')
    f.write(html)
```
**注：**read() 方法返回的是一个 bytes 类型的数据，需要通过 decode() 来转换成 str 类型。encoding 用于设置文本文档的编码格式，数据编码必须与文本文档编码一致，否则会出现乱码。

### 2. 复杂的请求
**urllib.request.Request** 的语法如下：
urllib.request.Request(url, data = None, headers = { }， method = None)

**参数解释：**
- **url：**完整的url格式，与 urllib.request.urlopen 的参数 url 一致。
- **data：**请求参数，与 urllib.request.urlopen 的参数 data 一致。
- **headers：**设置 request 请求头信息。
- **method：**设定请求方式，主要是 POST 和 GET 方式。

**示例：**
```
import urllib.request
url = 'https://movie.douban.com/'
# 自定义请求头
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/71.0.3578.80 Safari/537.36',
    'Referer': 'https://movie.douban.com/',
    'Connection': 'keep-alive'
}
# 设置 request 的请求头
req = urllib.request.Request(url, headers = headers)
# 使用 urlopen 打开 req
html = urllib.request.urlopen(req).read().decode('utf-8')
# 写入文件
with open('html.txt', 'w', encoding = 'utf-8')
    f.write(html)
```
### 3. 代理 IP

**原理：**以本机先访问代理IP, 在通过IP地址访问互联网，这样网站（服务器）接收到的访问IP就是代理IP地址。

Urllib 提供了 **urllib.request.ProxyHandler()** 方法可动态设置代理 IP 池，代理 IP 主要以字典格式写入方法。完成代理 IP 设置后，将代理好的 IP 写入 **urllib.request.build_opener()** 方法，生成对象 **opener**，然后通过 **opener** 的 **open()** 方法向网站（服务器）发送请求。

**示例：**
```
import urllib.request
url = 'https://movie.douban.com/'
#设置代理 IP
proxy_handler = urllib.request.ProxyHandler({
    'http': '119.180.152.97:8060',
    'http': '119.180.157.246:8060'
})
#必须使用 builde_opener() 函数来创建有代理 IP 功能的 opener 对象
opener = urllib.request.build_opener(proxy_handler)
responce = opener.open(url)
html = responce.read().decode('utf-8')
with open('html_2.txt', 'w', encoding = 'utf-8') as f:
    f.write(html)
```
### 4. 使用 Cookies
&emsp;&emsp;Cookies 主要用于获取用户登录信息，比如，通过提交数据实现用户登录之后，会生成带有登录状态的 Cookies，这时可以将 Cookies 保存在本地文件中，下次程序运行的时候，可以直接读取 Cookies 文件来实现用户登录。
&emsp;&emsp;Urllib 提供 **HTTPCookieProcessor()** 对 Cookies 操作。但 Cookies 的读写是由 **MozillaCookieJar()** 完成的。

**示例**
1.实现 Cookies 写入文件
```
import urllib.request
from http import cookiejar
filename = 'cookie.txt'
# MozillaCookieJar 保存 cookie
cookie = cookiejar.MozillaCookieJar(filename)
# HTTPCookieProcessor 创建 cookie 处理器
handler = urllib.request.HTTPCookieProcessor(cookie)
# 创建自定义 opener
opener = urllib.request.build_opener(handler)
responce = opener.open('https://movie.douban.com/')
cookie.save()
```
2.实现读取 Cookies
```
import urllib.request
from http import cookiejar
filename = 'cookie.txt'
# 创建 MozillaCookieJar 对象
cookie = cookiejar.MozillaCookieJar()
# 读取 cookie 内容到变量
cookie.load(filename)
# HTTPCookieProcessor 创建 cookie 处理器
handler = urllib.request.HTTPCookieProcessor(cookie)
opener = urllib.request.build_opener(handler)
responce = opener.open('https://movie.douban.com/')
print(cookie)
```
在保存和读取 Cookies 时可以设置参数，使 Cookies 信息隐藏在文件中。方法如下：
**cookie.save(ignore_discard = True, ignore_expires = True)
cookie.load(filename, ignore_discard = True, ignore_expires = True)**
### 5. 证书验证
&emsp;&emsp;当遇到一些特殊的网站时，在浏览器上会显示连接不是私密连接而导致无法浏览该网页。一些特殊的网站会使用自己的证书，如 12306 首页提示下载安装根证书，这时为了确保网站的数据在传输过程中的安全性。**urllib.request.urlopen** 该方法带有**cafile**、**capath**、**cadefault** 参数，可以用于设置用户的 **CA** 证书。
&emsp;&emsp;而遇到这类验证证书的网站，最简单而暴力的方法是直接关闭证书验证，可以在代码中引入 **SSL** 模块，设置关闭证书验证即可。

**示例**
```
import urllib.request
import ssl
# 关闭证书验证
ssl._create_default_https_context = ssl._create_unverified_context
responce = urllib.request.urlopen('https://kyfw.12306.cn/otn/leftTicket/init')
# 输出状态码
print(responce.getcode())
```
### 6. 数据处理
&emsp;&emsp;我们知道 **urllib.request.urlopen()** 方法是不区分请求方式的，识别请求方式主要通过参数 **data** 是否为 **None**。如果向服务器发送 **POST** 请求，那么参数 **data** 需要使用 **urllib.parse** 对参数内容进行处理。
&emsp;&emsp;**Urllib** 在请求访问服务器的时候，如果发生数据传递，就需要对内容进行编码处理，将包含 **str** 或 **bytes** 对象的两个元素元组序列转换为百分比编码的 **ASCII** 文本字符串。如果字符串要用作 **POST**，那么它应该被编码为字节，否则会导致 **TypeEror** 错误。

**示例**
```
import urllib.request
import urllib.parse
url = 'https://movie.douban.com/'
data = {
    'value': 'true'
}
# 数据处理
data = urllib.parse.urlencode(data).encode('utf-8')
req = urllib.request.Request(url, data)
responce = urllib.request.urlopen(req)
print(responce.getcode())
```
编码格式要根据网站的编码格式来确定。
除此之外，Urllib 还提供 quote() 和 unquote() 对 URL 编码处理，使用方法如下：
```
import urllib.parse
url = '#编程#'
# 第一次编码
first = urllib.parse.quote(url)
# 第二次编码
second = urllib.parse.quote(first)
print('第一次编码：', first)
print('第二次编码：', second)
# 第一次解码
first_r = urllib.parse.unquote(second)
# 第一次解码
second_r = urllib.parse.unquote(first_r)
print('第一次解码：', first_r)
print('第二次解码：', second_r)
```
