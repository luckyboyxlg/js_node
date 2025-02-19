# 七、urllib与requests

## 一、urllib的学习

##### 学习目标

了解urllib的基本使用 

------

### 1、urllib介绍

除了requests模块可以发送请求之外, urllib模块也可以实现请求的发送,只是操作方法略有不同!

urllib在python中分为urllib和urllib2，在python3中为urllib

下面以python3的urllib为例进行讲解

### 2、urllib的基本方法介绍

#### 2.1 urllib.Request

1. 构造简单请求

   ```python
   import urllib
   #构造请求
   request = urllib.request.Request("http://www.baidu.com")
   #发送请求获取响应
   response = urllib.request.urlopen(request)
   ```

2. 传入headers参数

   ```python
   import urllib
   #构造headers
   headers = {"User-Agent" : "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)"} 
   #构造请求
   request = urllib.request.Request(url, headers = headers)
   #发送请求
   response = urllib.request.urlopen(request)
   ```

3. 传入data参数 实现发送post请求

   ```python
   #构造headers
   headers={"User-Agent": "Mozilla...."}
   #构造请求体
   formdata = {
     "type":"AUTO",
     "i":"i love python",
     "doctype":"json",
   }
   #构造请求
   request = urllib.request.Request(url, data = data, headers = headers)
   #构造请求
   response = urllib.request.urlopen(request)
   print(response.read())
   ```

#### 2.2 response.read()

获取响应的html字符串,bytes类型

```python
#发送请求
response = urllib.request.urlopen("http://www.baidu.com")
#获取响应
response.read()
```

### 3、urllib请求百度首页的完整例子

```python
# coding=utf-8
import urllib

url = 'http://www.baidu.com'
#构造headers
headers = {"User-Agent" : "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)"}
#构造请求
request = urllib.request.Request(url, headers = headers)
#发送请求
response = urllib.request.urlopen(request)
#获取html字符串
html_str = response.read().decode()
print(html_str)
```

### 4、小结

1. urllib.request中实现了构造请求和发送请求的方法
2. urllib.request.Request(url,headers,data)能够构造请求
3. urllib.request.urlopen能够接受request请求或者url地址发送请求，获取响应
4. response.read()能够实现获取响应中的bytes字符串



# requests模块的入门使用

## 一、requests模块的入门使用

##### 学习目标：

1. 了解 requests模块的介绍
2. 掌握 requests的基本使用
3. 掌握 response常见的属性
4. 掌握 requests.text和content的区别
5. 掌握 解决网页的解码问题
6. 掌握 requests模块发送带headers的请求
7. 掌握 requests模块发送带参数的get请求

------

### 1、为什么要重点学习requests模块，而不是urllib

- 企业中用的最多的就是requests
- requests的底层实现就是urllib
- requests在python2 和python3中通用，方法完全一样
- requests简单易用
- requests能够自动帮助我们解压(gzip压缩的等)响应内容

### 2、requests的作用

作用：发送网络请求，返回响应数据

安装：pip install requests

### 3、requests模块发送简单的get请求、获取响应

需求：通过requests向百度首页发送请求，获取百度首页的数据

```python
import requests

# 目标url
url = 'https://www.baidu.com'

# 向目标url发送get请求
response = requests.get(url)

# 打印响应内容
print(response.text)
```

**response的常用属性：**

- `response.text` 响应体 str类型

- `response.encoding`  从HTTP　header中猜测的响应内容的编码方式

- `respones.content` 响应体 bytes类型

- `response.status_code` 响应状态码

- `response.request.headers` 响应对应的请求头

- `response.request.get('Cookie ')` 获取请求的cookie 

- `response.headers` 响应头

- `response.request._cookies` 响应对应请求的cookie 返回对象

- `response.cookies` 响应的cookie（经过了set-cookie动作）

- `response.url ` 获取访问的url

- `response.json()`  获取json数据 得到内容为字典 (如果接口响应体的格式是json格式时)

- `response.ok` 

  如果status_code小于200，response.ok返回True。

  如果status_code大于200，response.ok返回False。

**思考：text是response的属性还是方法呢？**

- 一般来说名词，往往都是对象的属性，对应的动词是对象的方法

##### 3.1 response.text 和response.content的区别

- `response.text`
  - 类型：str
  - 解码类型： requests模块自动根据HTTP 头部对响应的编码作出有根据的推测，推测的文本编码
  - 如何修改编码方式：`response.encoding="gbk/UTF-8"`
- `response.content`
  - 类型：bytes
  - 解码类型： 没有指定
  - 如何修改编码方式：`response.content.deocde("utf8")`

获取网页源码的通用方式：

1. `response.content.decode()`
2. `response.content.decode("GBK")`
3. `response.text`

以上三种方法从前往后尝试，能够100%的解决所有网页解码的问题

所以：更推荐使用`response.content.deocde()`的方式获取响应的html页面

##### 3.2 练习：把网络上的图片保存到本地

> 我们来把`www.baidu.com`的图片保存到本地

**思考：**

- 以什么方式打开文件
- 保存什么格式的内容

**分析：**

- 图片的url: https://www.baidu.com/img/bd_logo1.png
- 利用requests模块发送请求获取响应
- 以2进制写入的方式打开文件，并将response响应的二进制内容写入

```python
import requests

# 图片的url
url = 'https://www.baidu.com/img/bd_logo1.png'

# 响应本身就是一个图片,并且是二进制类型
response = requests.get(url)

# print(response.content)

# 以二进制+写入的方式打开文件
with open('baidu.png', 'wb') as f:
    # 写入response.content bytes二进制类型
    f.write(response.content)
```

### 4、发送带header的请求

> 我们先写一个获取百度首页的代码

```python
import requests

url = 'https://www.baidu.com'

response = requests.get(url)

print(response.content)

# 打印响应对应请求的请求头信息
print(response.request.headers)
```

##### 4.1 思考

对比浏览器上百度首页的网页源码和代码中的百度首页的源码，有什么不同？

代码中的百度首页的源码非常少，为什么？

##### 4.2 为什么请求需要带上header？

模拟浏览器，欺骗服务器，获取和浏览器一致的内容

##### 4.3 header的形式：字典

```python
headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36"}
```

##### 4.4 用法

```python
requests.get(url, headers=headers)
```

##### 4.5 完整的代码

```python
import requests

url = 'https://www.baidu.com'

headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36"}

# 在请求头中带上User-Agent，模拟浏览器发送请求
response = requests.get(url, headers=headers)

# print(response.content)

# 打印请求头信息
print(response.request.headers)
```

### 5、发送带参数的请求

> 我们在使用百度搜索的时候经常发现url地址中会有一个 `?`，那么该问号后边的就是请求参数，又叫做查询字符串

##### 5.1 什么叫做请求参数：

例1： http://www.webkaka.com/tutorial/server/2015/021013/

例2：https://www.baidu.com/s?wd=python&a=c

例1中没有请求参数！例2中?后边的就是请求参数

##### 5.2 请求参数的形式：字典

```python
kw = {'wd':'长城'}
```

##### 5.3 请求参数的用法

```python
requests.get(url,params=kw)
```

##### 5.4 关于参数的注意点

在url地址中， 很多参数是没有用的，比如百度搜索的url地址，其中参数只有一个字段有用，其他的都可以删除 如何确定那些请求参数有用或者没用：挨个尝试！ 对应的,在后续的爬虫中，越到很多参数的url地址，都可以尝试删除参数

##### 5.5 两种方式：发送带参数的请求

+ 对`https://www.baidu.com/s?wd=python`发起请求可以使用`requests.get(url, params=kw)`的方式

  ```python
  # 方式一：利用params参数发送带参数的请求
  import requests
  
  headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36"}
  
  # 这是目标url
  # url = 'https://www.baidu.com/s?wd=python'
  
  # 最后有没有问号结果都一样
  url = 'https://www.baidu.com/s?'
  
  # 请求参数是一个字典 即wd=python
  kw = {'wd': 'python'}
  
  # 带上请求参数发起请求，获取响应
  response = requests.get(url, headers=headers, params=kw)
  
  # 当有多个请求参数时，requests接收的params参数为多个键值对的字典，比如 '?wd=python&a=c'-->{'wd': 'python', 'a': 'c'}
  
  print(response.content)
  ```

+ 也可以直接对`https://www.baidu.com/s?wd=python`完整的url直接发送请求，不使用params参数

  ```python
  # 方式二：直接发送带参数的url的请求
  import requests
  
  headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36"}
  
  url = 'https://www.baidu.com/s?wd=python'
  
  # kw = {'wd': 'python'}
  
  # url中包含了请求参数，所以此时无需params
  response = requests.get(url, headers=headers)
  ```

### 6、作业

1.获取新浪首页，查看response.text 和response.content.decode()的区别

2.实现任意贴吧的爬虫，保存网页到本地

### 7、小结

1. requests模块的介绍：能够帮助我们发起请求获取响应
2. requests的基本使用：`requests.get(url)`
3. 以及response常见的属性：
   - `response.text` 响应体 str类型
   - `respones.content` 响应体 bytes类型
   - `response.status_code` 响应状态码
   - `response.request.headers` 响应对应的请求头
   - `response.headers` 响应头
   - `response.request._cookies` 响应对应请求的cookie
   - `response.cookies` 响应的cookie（经过了set-cookie动作）
4. 掌握 requests.text和content的区别：text返回str类型，content返回bytes类型
5. 掌握 解决网页的解码问题：
   - `response.content.decode()`
   - `response.content.decode("GBK")`
   - `response.text`
6. 掌握 requests模块发送带headers的请求：`requests.get(url, headers={})`
7. 掌握 requests模块发送带参数的get请求：`requests.get(url, params={})`



## 二、requests模块的深入使用

##### 学习目标：

1. 能够应用requests发送post请求的方法
2. 能够应用requests模块使用代理的方法
3. 了解代理ip的分类

------

### 1、使用requests发送POST请求

> 思考：哪些地方我们会用到POST请求？

1. 登录注册（ POST 比 GET 更安全）
2. 需要传输大文本内容的时候（ POST 请求对数据长度没有要求）

所以同样的，我们的爬虫也需要在这两个地方回去模拟浏览器发送post请求

##### 1.1 requests发送post请求语法：

- 用法：

  ```python
  response = requests.post("http://www.baidu.com/", data = data, headers=headers)
  ```

- data 的形式：字典

##### 1.2 POST请求练习

下面面我们通过金山翻译的例子看看post请求如何使用：

地址：https://www.iciba.com/fy

##### 思路分析

1. 抓包确定请求的url地址

   ![截屏2022-04-20 下午3.22.11](./imgs/07、urllib与requests.assets/%E6%88%AA%E5%B1%8F2022-04-20%20%E4%B8%8B%E5%8D%883.22.11.png)

   

2. 确定请求的参数

   ![截屏2022-04-20 下午3.23.07](./imgs/07、urllib与requests.assets/%E6%88%AA%E5%B1%8F2022-04-20%20%E4%B8%8B%E5%8D%883.23.07.png)

3. 确定返回数据的位置

   ![image-20220420152404175](./imgs/07、urllib与requests.assets/image-20220420152404175.png)

4. 模拟浏览器获取数据

   ```python
   import requests
   import json
   
   headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36"}
   
   url = 'https://ifanyi.iciba.com/index.php?c=trans&m=fy&client=6&auth_user=key_ciba&sign=99730f3bf66b2582'
   
   data = {
       'from': 'zh',
       'to': 'en',
       'q': 'lucky 是一个帅气的老师'
   }
   
   res = requests.post(url, headers=headers, data=data)
   # print(res.status_code)
   
   # 返回的是json字符串 需要在进行转换为字典
   data = json.loads(res.content.decode('UTF-8'))
   # print(type(data))
   print(data)
   print(data['content']['out'])
   ```

#### 1.3 小结

在模拟登陆等场景，经常需要发送post请求，直接使用`requests.post(url,data)`即可

### 2、使用代理

##### 2.1 为什么要使用代理

1. 让服务器以为不是同一个客户端在请求
2. 防止我们的真实地址被泄露，防止被追究

##### 2.2 理解使用代理的过程

![使用代理的过程](./imgs/07、urllib与requests.assets/%E4%BD%BF%E7%94%A8%E4%BB%A3%E7%90%86%E7%9A%84%E8%BF%87%E7%A8%8B.png)

##### 2.3 理解正向代理和反向代理的区别

![正向代理和反向代理的区别](./imgs/07、urllib与requests.assets/%E6%AD%A3%E5%90%91%E4%BB%A3%E7%90%86%E5%92%8C%E5%8F%8D%E5%90%91%E4%BB%A3%E7%90%86%E7%9A%84%E5%8C%BA%E5%88%AB.png)

通过上图可以看出：

- 正向代理：对于浏览器知道服务器的真实地址，例如VPN
- 反向代理：浏览器不知道服务器的真实地址，例如nginx

**详细讲解：**

正向代理是客户端与正向代理客户端在同一局域网，客户端发出请求，正向代理 替代客户端向服务器发出请求。服务器不知道谁是真正的客户端，正向代理隐藏了真实的请求客户端。
反向代理：服务器与反向代理在同一个局域网，客服端发出请求，反向代理接收请求 ，反向代理服务器会把我们的请求分转发到真实提供服务的各台服务器Nginx就是性能非常好的反向代理服务器，用来做负载均衡

![20190715113403480](./imgs/07、urllib与requests.assets/20190715113403480.png)

##### 2.4 代理的使用

- 用法：  proxies[ˈprɑksiz] 

  ```python
  requests.get("http://httpbin.org/ip",  proxies = proxies)
  ```

- proxies的形式：字典

- 例如：

  ```json
   proxies = {
        "http": "http://12.34.56.79:9527",
        "https": "https://12.34.56.79:9527",
  }
  ```

##### 2.5 代理IP的分类

根据代理ip的匿名程度，代理IP可以分为下面四类：

- 透明代理(Transparent Proxy)：透明代理的意思是客户端根本不需要知道有代理服务器的存在，但是它传送的仍然是真实的IP。使用透明代理时，对方服务器是可以知道你使用了代理的，并且他们也知道你的真实IP。你要想隐藏的话，不要用这个。透明代理为什么无法隐藏身份呢?因为他们将你的真实IP发送给了对方服务器，所以无法达到保护真实信息。
- 匿名代理(Anonymous Proxy)：匿名代理隐藏了您的真实IP，但是向访问对象可以检测是使用代理服务器访问他们的。会改变我们的请求信息，服务器端有可能会认为我们使用了代理。不过使用此种代理时，虽然被访问的网站不能知道你的ip地址，但仍然可以知道你在使用代理，当然某些能够侦测ip的网页也是可以查到你的ip。（https://wenku.baidu.com/view/9bf7b5bd3a3567ec102de2bd960590c69fc3d8cf.html）
- 高匿代理(Elite proxy或High Anonymity Proxy)：高匿名代理不改变客户机的请求，这样在服务器看来就像有个真正的客户浏览器在访问它，这时客户的真实IP是隐藏的，完全用代理服务器的信息替代了您的所有信息，就象您就是完全使用那台代理服务器直接访问对象，同时服务器端不会认为我们使用了代理。IPDIEA覆盖全球240＋国家地区ip高匿名代理不必担心被追踪。

在使用的使用，毫无疑问使用高匿代理效果最好

从请求使用的协议可以分为：

- http代理
- https代理
- socket代理等

不同分类的代理，在使用的时候需要根据抓取网站的协议来选择

##### 2.6 代理IP使用的注意点

- 反反爬

  使用代理ip是非常必要的一种`反反爬`的方式

  但是即使使用了代理ip，对方服务器任然会有很多的方式来检测我们是否是一个爬虫，比如：

  - 一段时间内，检测IP访问的频率，访问太多频繁会屏蔽

  - 检查Cookie，User-Agent，Referer等header参数，若没有则屏蔽

  - 服务方购买所有代理提供商，加入到反爬虫数据库里，若检测是代理则屏蔽

    所以更好的方式在使用代理ip的时候使用随机的方式进行选择使用，不要每次都用一个代理ip

- 代理ip池的更新

  购买的代理ip很多时候大部分(超过60%)可能都没办法使用，这个时候就需要通过程序去检测哪些可用，把不能用的删除掉。

+ 代理服务器平台的使用：

  当然还有很多免费的，但是大多都不可用需要自己尝试

  + http://www.66ip.cn
  + https://ip.jiangxianli.com/?page=1
  + https://www.zdaye.com
  + https://www.kuaidaili.com/free

### 3、配置

+ 浏览器配置代理

  右边三点\==> 设置\==> 高级\==> 代理\==> 局域网设置\==> 为LAN使用代理==> 输入ip和端口号即可

  参考网址：https://jingyan.baidu.com/article/a681b0dece76407a1843468d.html

+ 代码配置

  urllib

  ```python
  handler = urllib.request.ProxyHandler({'http': '114.215.95.188:3128'})
  opener = urllib.request.build_opener(handler)
  # 后续都使用opener.open方法去发送请求即可
  ```

  requests

  ```python
  # 用到的库
  import requests
  # 写入获取到的ip地址到proxy
  # 一个ip地址
  proxy = {
      'http':'http://221.178.232.130:8080'
  }
  """
  # 多个ip地址
  proxy = [
    {'http':'http://221.178.232.130:8080'},
    {'http':'http://221.178.232.130:8080'}
  ]
  import random
  proxy = random.choice(proxy)
  """
  
  # 使用代理
  proxy = {
      'http': 'http://58.20.184.187:9091'
  }
  # 两种方式都可以
  """
  proxy = {
      'http': '58.20.184.187:9091'
  }
  """
  result = requests.get("http://httpbin.org/ip", proxies=proxy)
  
  print(result.text)
  ```

### 4、小结

1. requests发送post请求使用requests.post方法，带上请求体，其中请求体需要时字典的形式，传递给data参数接收
2. 在requests中使用代理，需要准备字典形式的代理，传递给proxies参数接收
3. 不同协议的url地址，需要使用不同的代理去请求



## 三、requests模块处理cookie相关的请求

**学习目标**

掌握requests处理cookie的三种方法

### 1 爬虫中使用cookie

> 为了能够通过爬虫获取到登录后的页面，或者是解决通过cookie的反扒，需要使用request来处理cookie相关的请求

##### 1.1 爬虫中使用cookie的利弊

+ 带上cookie的好处
  + 能够访问登录后的页面
  + 能够实现部分反反爬
+ 带上cookie的坏处
  + 一套cookie往往对应的是一个用户的信息，请求太频繁有更大的可能性被对方识别为爬虫
  + 那么上面的问题如何解决 ?使用多个账号

##### 1.2 requests处理cookie的方法

使用requests处理cookie有三种方法：
+ cookie字符串放在headers中
+ 把cookie字典放传给请求方法的cookies参数接收
+ 使用requests提供的session模块

### 2、cookie添加在heades中

##### 2.1 headers中cookie的位置

![headers中的cookie](./imgs/07、urllib与requests.assets/headers%E4%B8%AD%E7%9A%84cookie.png)

- headers中的cookie：
  - 使用分号(;)隔开
  - 分号两边的类似a=b形式的表示一条cookie
  - a=b中，a表示键（name），b表示值（value）
  - 在headers中仅仅使用了cookie的name和value

##### 2.2 cookie的具体组成的字段

![cookie的具体字段](./imgs/07、urllib与requests.assets/cookie%E7%9A%84%E5%85%B7%E4%BD%93%E5%AD%97%E6%AE%B5.png)

由于headers中对cookie仅仅使用它的name和value，所以在代码中我们仅仅需要cookie的name和value即可

##### 2.3 在headers中使用cookie

复制浏览器中的cookie到代码中使用

```python
headers = {
"User-Agent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36",
"Cookie":" Pycharm-26c2d973=dbb9b300-2483-478f-9f5a-16ca4580177e; Hm_lvt_98b9d8c2fd6608d564bf2ac2ae642948=1512607763; Pycharm-26c2d974=f645329f-338e-486c-82c2-29e2a0205c74; _xsrf=2|d1a3d8ea|c5b07851cbce048bd5453846445de19d|1522379036"}

requests.get(url,headers=headers)
```

##### 注意：

cookie有过期时间 ，所以直接复制浏览器中的cookie可能意味着下一程序继续运行的时候需要替换代码中的cookie，对应的我们也可以通过一个程序专门来获取cookie供其他程序使用；当然也有很多网站的cookie过期时间很长，这种情况下，直接复制cookie来使用更加简单



### 3、使用cookies参数接收字典形式的cookie

- cookies的形式：字典

```json
cookies = {"cookie的name":"cookie的value"}
```

- 使用方法：

```python
requests.get(url,headers=headers,cookies=cookie_dict}
```

+ 实例（爬取雪球网）

  https://xueqiu.com/

  在网络中找到当前请求的网址 点击cookies 将当前的k,value复制到代码中

  ![image-20220420190426741](./imgs/07、urllib与requests.assets/image-20220420190426741.png)
  
  ```python
  cookie_dict = {
      'u': '1990923459',
      'bid': '1f110dfd43538f4b8362dfcd21ffbb64_l27g4lfl',
      'xq_is_login': '1',
      'xq_r_token': '5dcbe83944f0b75325f91246061d4a2a01999367'
  }
  ```

  完整代码
  
  ```python
  import requests
  
  # 携带cookie登录雪球网  抓取完善个人资料页面
  headers = {
      'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.127 Safari/537.36',
      'Referer': 'https://xueqiu.com/u/1990923459',
      'Host': 'xueqiu.com',
  }
  url = 'https://xueqiu.com/users/connectnew?redirect=/setting/user'
  
  cookie_dict = {
      'u': '1990923459',
      'bid': '1f110dfd43538f4b8362dfcd21ffbb64_l27g4lfl',
      'xq_is_login': '1',
      'xq_r_token': '5dcbe83944f0b75325f91246061d4a2a01999367'
  }
  res = requests.get(url, headers=headers, cookies=cookie_dict)
  with open('雪球网.html', 'w') as f:
      f.write(res.content.decode('UTF-8'))
      print(res.content.decode('UTF-8'))
  ```

  成果
  
  ![image-20220420190612671](./imgs/07、urllib与requests.assets/image-20220420190612671.png)

### 4、使用requests.session处理cookie

> 前面使用手动的方式使用cookie，那么有没有更好的方法在requets中处理cookie呢？

requests 提供了一个叫做session类，来实现客户端和服务端的`会话保持`

会话保持有两个内涵：

- 保存cookie，下一次请求会带上前一次的cookie
- 实现和服务端的长连接，加快请求速度

##### 4.1 使用方法

```python
session = requests.session()
response = session.get(url,headers)
```

session实例在请求了一个网站后，对方服务器设置在本地的cookie会保存在session中，下一次再使用session请求对方服务器的时候，会带上前一次的cookie

##### 4.2 使用代理

```python
poixes = {'http': '61.150.96.27:368801'}
req = sess.get(url,headers=headers, proxies=poixes)
```

实例

```python
from lxml import etree
import requests
url = 'https://xueqiu.com/statuses/hot/listV2.json?since_id=-1&max_id=364193&size=15'
# 手动携带cookie进行请求
headers = {
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/15.0 Safari/605.1.15',
}
sess = requests.Session() #创建了一个session对象
#尝试基于sess发起请求动态捕获cookie
main_url = 'https://xueqiu.com/'
sess.get(main_url,headers=headers)
poixes = {'http': '61.150.96.27:368801'}
req = sess.get(url,headers=headers, proxies=poixes)  #携带了cookie进行请求发送
for i in req.json()['items']:
    print(i['original_status']['description'])
```

##### 4.3 设置

adapters[əˈdæptərz]

```python
# 频繁请求某个网址偶尔会报错请求超时，可采用下面方式降低失败率，设置重试次数为5，会话设置为不维持连接.
requests.adapters.DEFAULT_RETRIES = 5
# 设置连接活跃状态为False
s = requests.session()
s.keep_alive = False
```

```python
from lxml import etree
import requests
url = 'https://xueqiu.com/statuses/hot/listV2.json?since_id=-1&max_id=364193&size=15'
# 手动携带cookie进行请求
headers = {
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/15.0 Safari/605.1.15',
}
# 频繁请求某个网址偶尔会报错请求超时，可采用下面方式降低失败率，设置重试次数为5，会话设置为不维持连接.
requests.adapters.DEFAULT_RETRIES = 5
sess = requests.Session() #创建了一个session对象
sess.keep_alive = False
#尝试基于sess发起请求动态捕获cookie
main_url = 'https://xueqiu.com/'
sess.get(main_url,headers=headers)

poixes = {'http': '61.150.96.27:368801'}
req = sess.get(url,headers=headers, proxies=poixes)  #携带了cookie进行请求发送
for i in req.json()['items']:
    print(i['original_status']['description'])
```

注意：如果爬取出现以下错误，可以使用如上配置

```python
HTTPConnectionPool(host='172.19.5.101', port=8085): Max retries exceeded with url: <url >(Caused by ConnectTimeoutError(<urllib3.connection.HTTPConnection object at 0x006DB1F0>, 'Connection to <server ip> timed out. (connect timeout=10)'))
```

基本上意思就是发出的网络请求比较频繁，requests内置的urllibs3不能及时的为我们断开连接，于是就有了以上报错。
代码的基本意思就是断开连接、增加重试次数。

##### 4.4 动手练习：模拟登陆

+ 17k小说网    https://passport.17k.com/

+ 古诗文：https://so.gushiwen.cn

+ 打码平台  

  图鉴   http://www.ttshitu.com/

##### 思路分析

1. 准备url地址和请求参数
2. 构造session发送post请求
3. 使用session请求个人主页，观察是否请求成功

### 5、小结

1. cookie字符串可以放在headers字典中，键为Cookie，值为cookie字符串
2. 可以把cookie字符串转化为字典，使用请求方法的cookies参数接收
3. 使用requests提供的session模块，能够自动实现cookie的处理，包括请求的时候携带cookie，获取响应的时候保存cookie



## 四、requests模块的其他方法

##### 学习目标

1. 掌握requests中cookirJar的处理方法
2. 掌握requests解决https证书错误的问题
3. 掌握requests中超时参数的使用
4. 掌握retrying模块的使用

### 1、requests中cookirJar的处理方法

> 使用request获取的resposne对象，具有cookies属性，能够获取对方服务器设置在本地的cookie，但是如何使用这些cookie呢？

##### 1.1 方法介绍

1. response.cookies是CookieJar类型
2. 使用requests.utils.dict_from_cookiejar，能够实现把cookiejar对象转化为字典

##### 1.2 方法展示

```python
import requests

url = "http://www.baidu.com"
#发送请求，获取resposne
response = requests.get(url)
print(type(response.cookies))

#使用方法从cookiejar中提取数据  等同于  dict(response.cookies)
cookies = requests.utils.dict_from_cookiejar(response.cookies)
print(cookies)
```

输出为:

```python
<class 'requests.cookies.RequestsCookieJar'>
{'BDORZ': '27315'}
```

##### 注意：

在前面的requests的session类中，我们不需要处理cookie的任何细节，如果有需要，我们可以使用上述方法来解决

### 2、requests处理证书错误

> 经常我们在网上冲浪时，经常能够看到下面的提示：

![12306ssl错误](./imgs/07、urllib与requests.assets/12306ssl%E9%94%99%E8%AF%AF.png)

出现这个问题的原因是：ssl的证书不安全导致

##### 2.1 代码中发起请求的效果

那么如果在代码中请求会怎么样呢？

```python
import requests

url = "https://www.12306.cn/mormhweb/"
response = requests.get(url)
```

返回证书错误，如下：

```
ssl.CertificateError ...
```

##### 2.2 解决方案

为了在代码中能够正常的请求，我们修改添加一个参数

```python
import requests

url = "https://www.12306.cn/mormhweb/"
response = requests.get(url,verify=False)
```

### 3、超时参数的使用

> 在平时网上冲浪的过程中，我们经常会遇到网络波动，这个时候，一个请求等了很久可能任然没有结果
>
> 在爬虫中，一个请求很久没有结果，就会让整个项目的效率变得非常低，这个时候我们就需要对请求进行强制要求，让他必须在特定的时间内返回结果，否则就报错

##### 3.1 超时参数使用方法如下：

```python
response = requests.get(url,timeout=3)
```

通过添加timeout参数，能够保证在3秒钟内返回响应，否则会报错

##### 注意：

这个方法还能够拿来检测代理ip的质量，如果一个代理ip在很长时间没有响应，那么添加超时之后也会报错，对应的这个ip就可以从代理ip池中删除

### 4、retrying模块的使用

> 使用超时参数能够加快我们整体的请求速度，但是在正常的网页浏览过成功，如果发生速度很慢的情况，我们会做的选择是**刷新页面**，那么在代码中，我们是否也可以刷新请求呢？

对应的，retrying模块就可以帮助我们解决

##### 4.1 retrying模块的使用

retrying模块的地址：https://pypi.org/project/retrying/

pip install retrying

retrying 模块的使用

1. 使用retrying模块提供的retry模块
2. 通过装饰器的方式使用，让被装饰的函数反复执行
3. retry中可以传入参数`stop_max_attempt_number`,让函数报错后继续重新执行，达到最大执行次数的上限，如果每次都报错，整个函数报错，如果中间有一个成功，程序继续往后执行

##### 4.2 retrying和requests的简单封装

实现一个发送请求的函数，每次爬虫中直接调用该函数即可实现发送请求，在其中

- 使用timeout实现超时报错
- 使用retrying模块实现重试

代码参考:

```python
# parse.py
import requests
from retrying import retry

headers = {}

#最大重试3次，3次全部报错，才会报错
@retry(stop_max_attempt_number=3) 
def _parse_url(url):
    #超时的时候回报错并重试
    response = requests.get(url, headers=headers, timeout=3) 
    #状态码不是200，也会报错并重试
    assert response.status_code == 200
    return response


def parse_url(url):
    try: #进行异常捕获
        response = _parse_url(url)
    except Exception as e:
        print(e)
        #报错返回None
        response = None
    return response
```

- stop_max_attempt_number：最大重试次数，超过该次数就停止重试  毫秒
-  stop_max_delay：最大延迟时间（执行这个方法重试的总时间），超过该时间就停止毫秒
-  wait_fixed：两次retrying之间的等待时间毫秒
-  wait_random_min和wait_random_max：用随机的方式产生两次retrying之间的等待时间毫秒
-  wait_incrementing_start和wait_incrementing_increment：每调用一次增加固定时长毫秒
-  wait_exponential_multiplier和wait_exponential_max：以指数的形式产生两次retrying之间的等待时间，产生的值为2^previous_attempt_number * wait_exponential_multiplier，previous_attempt_number是前面已经retry的次数，如果产生的这个值超过了wait_exponential_max的大小，那么之后两个retrying之间的停留值都为wait_exponential_max。

### 5、fake_useragent模块

作用：随机返回User-Agent

安装： pip install fake_useragent

使用：

```python
from fake_useragent import UserAgent

# 指定浏览器名称
ua = UserAgent()
print(ua.ie)
print(ua.firefox)
print(ua.chrome)
```

注意：如果使用过程中报如下错误

```
fake_useragent Maximum amount of retries reached...
```

解决：

​    第一步 准备需要的 json 文件：

​    创建一个 json 文件或者直接下载需要的 json 文件，json 文件的内容（下载连接）我放到文章底部可以通过复制自己写，也可以直接下载文件使用；

 准备好 json 文件后将 json 文件放到与 fake.py 文件同一文件夹里面（如上图）；文件位置找到方法：

Ctrl 点击 UserAgent → 点击文件右键 → 打开于 文件路径 （或者Ctrl + Alt +F12） → 点击  fake.py 文件  

第二步  改写函数代码：

找到  FakeUserAgent 类函数， 将函数的 __init__ 里面的 path 值进行更改 添加一个变量，将变量赋值给 path；值必须是绝对路径；写死不能改即可。

![image-20220629](./imgs/07、urllib与requests.assets/image-20220629-9879748.png)

User-agent.json

```
{"browsers": {"chrome": ["Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.77 Safari/537.36", "Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/537.36 (KHTML like Gecko) Chrome/44.0.2403.155 Safari/537.36", "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2228.0 Safari/537.36", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2227.1 Safari/537.36", "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2227.0 Safari/537.36", "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2227.0 Safari/537.36", "Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2226.0 Safari/537.36", "Mozilla/5.0 (Windows NT 6.4; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2225.0 Safari/537.36", "Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2225.0 Safari/537.36", "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2224.3 Safari/537.36", "Mozilla/5.0 (Windows NT 10.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/40.0.2214.93 Safari/537.36", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/37.0.2062.124 Safari/537.36", "Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/37.0.2049.0 Safari/537.36", "Mozilla/5.0 (Windows NT 4.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/37.0.2049.0 Safari/537.36", "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.67 Safari/537.36", "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.67 Safari/537.36", "Mozilla/5.0 (X11; OpenBSD i386) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.125 Safari/537.36", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1944.0 Safari/537.36", "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/35.0.3319.102 Safari/537.36", "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/35.0.2309.372 Safari/537.36", "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/35.0.2117.157 Safari/537.36", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/35.0.1916.47 Safari/537.36", "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/34.0.1866.237 Safari/537.36", "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/34.0.1847.137 Safari/4E423F", "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/34.0.1847.116 Safari/537.36 Mozilla/5.0 (iPad; U; CPU OS 3_2 like Mac OS X; en-us) AppleWebKit/531.21.10 (KHTML, like Gecko) Version/4.0.4 Mobile/7B334b Safari/531.21.10", "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/33.0.1750.517 Safari/537.36", "Mozilla/5.0 (Windows NT 6.2; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1667.0 Safari/537.36", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1664.3 Safari/537.36", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1664.3 Safari/537.36", "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/31.0.1650.16 Safari/537.36", "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/31.0.1623.0 Safari/537.36", "Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/30.0.1599.17 Safari/537.36", "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/29.0.1547.62 Safari/537.36", "Mozilla/5.0 (X11; CrOS i686 4319.74.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/29.0.1547.57 Safari/537.36", "Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/29.0.1547.2 Safari/537.36", "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/28.0.1468.0 Safari/537.36", "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/28.0.1467.0 Safari/537.36", "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/28.0.1464.0 Safari/537.36", "Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1500.55 Safari/537.36", "Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.93 Safari/537.36", "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.93 Safari/537.36", "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.93 Safari/537.36", "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.93 Safari/537.36", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.93 Safari/537.36", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_7_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.93 Safari/537.36", "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.90 Safari/537.36", "Mozilla/5.0 (X11; NetBSD) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.116 Safari/537.36", "Mozilla/5.0 (X11; CrOS i686 3912.101.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.116 Safari/537.36", "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.17 (KHTML, like Gecko) Chrome/24.0.1312.60 Safari/537.17", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_2) AppleWebKit/537.17 (KHTML, like Gecko) Chrome/24.0.1309.0 Safari/537.17"], "internetexplorer": ["Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko", "Mozilla/5.0 (compatible, MSIE 11, Windows NT 6.3; Trident/7.0;  rv:11.0) like Gecko", "Mozilla/5.0 (compatible; MSIE 10.6; Windows NT 6.1; Trident/5.0; InfoPath.2; SLCC1; .NET CLR 3.0.4506.2152; .NET CLR 3.5.30729; .NET CLR 2.0.50727) 3gpp-gba UNTRUSTED/1.0", "Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 7.0; InfoPath.3; .NET CLR 3.1.40767; Trident/6.0; en-IN)", "Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.1; WOW64; Trident/6.0)", "Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.1; Trident/6.0)", "Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.1; Trident/5.0)", "Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.1; Trident/4.0; InfoPath.2; SV1; .NET CLR 2.0.50727; WOW64)", "Mozilla/5.0 (compatible; MSIE 10.0; Macintosh; Intel Mac OS X 10_7_3; Trident/6.0)", "Mozilla/4.0 (Compatible; MSIE 8.0; Windows NT 5.2; Trident/6.0)", "Mozilla/4.0 (compatible; MSIE 10.0; Windows NT 6.1; Trident/5.0)", "Mozilla/1.22 (compatible; MSIE 10.0; Windows 3.1)", "Mozilla/5.0 (Windows; U; MSIE 9.0; WIndows NT 9.0; en-US))", "Mozilla/5.0 (Windows; U; MSIE 9.0; Windows NT 9.0; en-US)", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 7.1; Trident/5.0)", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; SLCC2; Media Center PC 6.0; InfoPath.3; MS-RTC LM 8; Zune 4.7)", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; SLCC2; Media Center PC 6.0; InfoPath.3; MS-RTC LM 8; Zune 4.7", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; Zune 4.0; InfoPath.3; MS-RTC LM 8; .NET4.0C; .NET4.0E)", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; chromeframe/12.0.742.112)", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; .NET CLR 3.5.30729; .NET CLR 3.0.30729; .NET CLR 2.0.50727; Media Center PC 6.0)", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Win64; x64; Trident/5.0; .NET CLR 3.5.30729; .NET CLR 3.0.30729; .NET CLR 2.0.50727; Media Center PC 6.0)", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Win64; x64; Trident/5.0; .NET CLR 2.0.50727; SLCC2; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; Zune 4.0; Tablet PC 2.0; InfoPath.3; .NET4.0C; .NET4.0E)", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Win64; x64; Trident/5.0", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0; yie8)", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; InfoPath.2; .NET CLR 1.1.4322; .NET4.0C; Tablet PC 2.0)", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0; FunWebProducts)", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0; chromeframe/13.0.782.215)", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0; chromeframe/11.0.696.57)", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0) chromeframe/10.0.648.205", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/4.0; GTB7.4; InfoPath.1; SV1; .NET CLR 2.8.52393; WOW64; en-US)", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.0; Trident/5.0; chromeframe/11.0.696.57)", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.0; Trident/4.0; GTB7.4; InfoPath.3; SV1; .NET CLR 3.1.76908; WOW64; en-US)", "Mozilla/5.0 (compatible; MSIE 8.0; Windows NT 6.1; Trident/4.0; GTB7.4; InfoPath.2; SV1; .NET CLR 3.3.69573; WOW64; en-US)", "Mozilla/5.0 (compatible; MSIE 8.0; Windows NT 6.0; Trident/4.0; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; .NET CLR 1.0.3705; .NET CLR 1.1.4322)", "Mozilla/5.0 (compatible; MSIE 8.0; Windows NT 6.0; Trident/4.0; InfoPath.1; SV1; .NET CLR 3.8.36217; WOW64; en-US)", "Mozilla/5.0 (compatible; MSIE 8.0; Windows NT 6.0; Trident/4.0; .NET CLR 2.7.58687; SLCC2; Media Center PC 5.0; Zune 3.4; Tablet PC 3.6; InfoPath.3)", "Mozilla/5.0 (compatible; MSIE 8.0; Windows NT 5.2; Trident/4.0; Media Center PC 4.0; SLCC1; .NET CLR 3.0.04320)", "Mozilla/5.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0; SLCC1; .NET CLR 3.0.4506.2152; .NET CLR 3.5.30729; .NET CLR 1.1.4322)", "Mozilla/5.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0; InfoPath.2; SLCC1; .NET CLR 3.0.4506.2152; .NET CLR 3.5.30729; .NET CLR 2.0.50727)", "Mozilla/5.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0; .NET CLR 1.1.4322; .NET CLR 2.0.50727)", "Mozilla/5.0 (compatible; MSIE 8.0; Windows NT 5.1; SLCC1; .NET CLR 1.1.4322)", "Mozilla/5.0 (compatible; MSIE 8.0; Windows NT 5.0; Trident/4.0; InfoPath.1; SV1; .NET CLR 3.0.4506.2152; .NET CLR 3.5.30729; .NET CLR 3.0.04506.30)", "Mozilla/5.0 (compatible; MSIE 7.0; Windows NT 5.0; Trident/4.0; FBSMTWB; .NET CLR 2.0.34861; .NET CLR 3.0.3746.3218; .NET CLR 3.5.33652; msn OptimizedIE8;ENUS)", "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.2; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0)", "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0; SLCC2; Media Center PC 6.0; InfoPath.2; MS-RTC LM 8)", "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0; SLCC2; Media Center PC 6.0; InfoPath.2; MS-RTC LM 8", "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; Media Center PC 6.0; .NET CLR 3.5.30729; .NET CLR 3.0.30729; .NET4.0C)", "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; InfoPath.3; .NET4.0C; .NET4.0E; .NET CLR 3.5.30729; .NET CLR 3.0.30729; MS-RTC LM 8)", "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; InfoPath.2)", "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; Zune 3.0)"], "firefox": ["Mozilla/5.0 (X11; Linux i686; rv:64.0) Gecko/20100101 Firefox/64.0", "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:64.0) Gecko/20100101 Firefox/64.0", "Mozilla/5.0 (X11; Linux i586; rv:63.0) Gecko/20100101 Firefox/63.0", "Mozilla/5.0 (Windows NT 6.2; WOW64; rv:63.0) Gecko/20100101 Firefox/63.0", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.10; rv:62.0) Gecko/20100101 Firefox/62.0", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:10.0) Gecko/20100101 Firefox/62.0", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.13; ko; rv:1.9.1b2) Gecko/20081201 Firefox/60.0", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Firefox/58.0.1", "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:54.0) Gecko/20100101 Firefox/58.0", "Mozilla/5.0 (Windows NT 6.3; WOW64; rv:52.59.12) Gecko/20160044 Firefox/52.59.12", "Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.9a1) Gecko/20060814 Firefox/51.0", "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:46.0) Gecko/20120121 Firefox/46.0", "Mozilla/5.0 (Windows NT 10.0; WOW64; rv:45.66.18) Gecko/20177177 Firefox/45.66.18", "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:40.0) Gecko/20100101 Firefox/40.1", "Mozilla/5.0 (Windows NT 6.3; rv:36.0) Gecko/20100101 Firefox/36.0", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10; rv:33.0) Gecko/20100101 Firefox/33.0", "Mozilla/5.0 (X11; Linux i586; rv:31.0) Gecko/20100101 Firefox/31.0", "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:31.0) Gecko/20130401 Firefox/31.0", "Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:28.0) Gecko/20100101 Firefox/31.0", "Mozilla/5.0 (Windows NT 5.1; rv:31.0) Gecko/20100101 Firefox/31.0", "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:29.0) Gecko/20120101 Firefox/29.0", "Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:25.0) Gecko/20100101 Firefox/29.0", "Mozilla/5.0 (X11; OpenBSD amd64; rv:28.0) Gecko/20100101 Firefox/28.0", "Mozilla/5.0 (X11; Linux x86_64; rv:28.0) Gecko/20100101  Firefox/28.0", "Mozilla/5.0 (Windows NT 6.1; rv:27.3) Gecko/20130101 Firefox/27.3", "Mozilla/5.0 (Windows NT 6.2; Win64; x64; rv:27.0) Gecko/20121011 Firefox/27.0", "Mozilla/5.0 (Windows NT 6.2; rv:20.0) Gecko/20121202 Firefox/26.0", "Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:25.0) Gecko/20100101 Firefox/25.0", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.6; rv:25.0) Gecko/20100101 Firefox/25.0", "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:24.0) Gecko/20100101 Firefox/24.0", "Mozilla/5.0 (Windows NT 6.0; WOW64; rv:24.0) Gecko/20100101 Firefox/24.0", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.8; rv:24.0) Gecko/20100101 Firefox/24.0", "Mozilla/5.0 (Windows NT 6.2; rv:22.0) Gecko/20130405 Firefox/23.0", "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:23.0) Gecko/20130406 Firefox/23.0", "Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:23.0) Gecko/20131011 Firefox/23.0", "Mozilla/5.0 (Windows NT 6.2; rv:22.0) Gecko/20130405 Firefox/22.0", "Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:22.0) Gecko/20130328 Firefox/22.0", "Mozilla/5.0 (Windows NT 6.1; rv:22.0) Gecko/20130405 Firefox/22.0", "Mozilla/5.0 (Microsoft Windows NT 6.2.9200.0); rv:22.0) Gecko/20130405 Firefox/22.0", "Mozilla/5.0 (Windows NT 6.2; Win64; x64; rv:16.0.1) Gecko/20121011 Firefox/21.0.1", "Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:16.0.1) Gecko/20121011 Firefox/21.0.1", "Mozilla/5.0 (Windows NT 6.2; Win64; x64; rv:21.0.0) Gecko/20121011 Firefox/21.0.0", "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:21.0) Gecko/20130331 Firefox/21.0", "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:21.0) Gecko/20100101 Firefox/21.0", "Mozilla/5.0 (X11; Linux i686; rv:21.0) Gecko/20100101 Firefox/21.0", "Mozilla/5.0 (Windows NT 6.2; WOW64; rv:21.0) Gecko/20130514 Firefox/21.0", "Mozilla/5.0 (Windows NT 6.2; rv:21.0) Gecko/20130326 Firefox/21.0", "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:21.0) Gecko/20130401 Firefox/21.0", "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:21.0) Gecko/20130331 Firefox/21.0", "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:21.0) Gecko/20130330 Firefox/21.0"], "safari": ["Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_3) AppleWebKit/537.75.14 (KHTML, like Gecko) Version/7.0.3 Safari/7046A194A", "Mozilla/5.0 (iPad; CPU OS 6_0 like Mac OS X) AppleWebKit/536.26 (KHTML, like Gecko) Version/6.0 Mobile/10A5355d Safari/8536.25", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_6_8) AppleWebKit/537.13+ (KHTML, like Gecko) Version/5.1.7 Safari/534.57.2", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_7_3) AppleWebKit/534.55.3 (KHTML, like Gecko) Version/5.1.3 Safari/534.53.10", "Mozilla/5.0 (iPad; CPU OS 5_1 like Mac OS X) AppleWebKit/534.46 (KHTML, like Gecko ) Version/5.1 Mobile/9B176 Safari/7534.48.3", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_8; de-at) AppleWebKit/533.21.1 (KHTML, like Gecko) Version/5.0.5 Safari/533.21.1", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_7; da-dk) AppleWebKit/533.21.1 (KHTML, like Gecko) Version/5.0.5 Safari/533.21.1", "Mozilla/5.0 (Windows; U; Windows NT 6.1; tr-TR) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Windows; U; Windows NT 6.1; ko-KR) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Windows; U; Windows NT 6.1; fr-FR) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Windows; U; Windows NT 6.1; cs-CZ) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Windows; U; Windows NT 6.0; ja-JP) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Windows; U; Windows NT 6.0; en-US) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Macintosh; U; PPC Mac OS X 10_5_8; zh-cn) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Macintosh; U; PPC Mac OS X 10_5_8; ja-jp) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_7; ja-jp) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_6; zh-cn) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_6; sv-se) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_6; ko-kr) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_6; ja-jp) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_6; it-it) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_6; fr-fr) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_6; es-es) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_6; en-us) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_6; en-gb) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_6; de-de) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27", "Mozilla/5.0 (Windows; U; Windows NT 6.1; sv-SE) AppleWebKit/533.19.4 (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4", "Mozilla/5.0 (Windows; U; Windows NT 6.1; ja-JP) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4", "Mozilla/5.0 (Windows; U; Windows NT 6.1; de-DE) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4", "Mozilla/5.0 (Windows; U; Windows NT 6.0; hu-HU) AppleWebKit/533.19.4 (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4", "Mozilla/5.0 (Windows; U; Windows NT 6.0; en-US) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4", "Mozilla/5.0 (Windows; U; Windows NT 6.0; de-DE) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4", "Mozilla/5.0 (Windows; U; Windows NT 5.1; ru-RU) AppleWebKit/533.19.4 (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4", "Mozilla/5.0 (Windows; U; Windows NT 5.1; ja-JP) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4", "Mozilla/5.0 (Windows; U; Windows NT 5.1; it-IT) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4", "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_7; en-us) AppleWebKit/534.16+ (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_6; fr-ch) AppleWebKit/533.19.4 (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_5; de-de) AppleWebKit/534.15+ (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_5; ar) AppleWebKit/533.19.4 (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4", "Mozilla/5.0 (Android 2.2; Windows; U; Windows NT 6.1; en-US) AppleWebKit/533.19.4 (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4", "Mozilla/5.0 (Windows; U; Windows NT 6.1; zh-HK) AppleWebKit/533.18.1 (KHTML, like Gecko) Version/5.0.2 Safari/533.18.5", "Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/533.19.4 (KHTML, like Gecko) Version/5.0.2 Safari/533.18.5", "Mozilla/5.0 (Windows; U; Windows NT 6.0; tr-TR) AppleWebKit/533.18.1 (KHTML, like Gecko) Version/5.0.2 Safari/533.18.5", "Mozilla/5.0 (Windows; U; Windows NT 6.0; nb-NO) AppleWebKit/533.18.1 (KHTML, like Gecko) Version/5.0.2 Safari/533.18.5", "Mozilla/5.0 (Windows; U; Windows NT 6.0; fr-FR) AppleWebKit/533.18.1 (KHTML, like Gecko) Version/5.0.2 Safari/533.18.5", "Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-TW) AppleWebKit/533.19.4 (KHTML, like Gecko) Version/5.0.2 Safari/533.18.5", "Mozilla/5.0 (Windows; U; Windows NT 5.1; ru-RU) AppleWebKit/533.18.1 (KHTML, like Gecko) Version/5.0.2 Safari/533.18.5", "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_5_8; zh-cn) AppleWebKit/533.18.1 (KHTML, like Gecko) Version/5.0.2 Safari/533.18.5"], "opera": ["Opera/9.80 (X11; Linux i686; Ubuntu/14.10) Presto/2.12.388 Version/12.16", "Opera/9.80 (Macintosh; Intel Mac OS X 10.14.1) Presto/2.12.388 Version/12.16", "Opera/9.80 (Windows NT 6.0) Presto/2.12.388 Version/12.14", "Mozilla/5.0 (Windows NT 6.0; rv:2.0) Gecko/20100101 Firefox/4.0 Opera 12.14", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.0) Opera 12.14", "Opera/12.80 (Windows NT 5.1; U; en) Presto/2.10.289 Version/12.02", "Opera/9.80 (Windows NT 6.1; U; es-ES) Presto/2.9.181 Version/12.00", "Opera/9.80 (Windows NT 5.1; U; zh-sg) Presto/2.9.181 Version/12.00", "Opera/12.0(Windows NT 5.2;U;en)Presto/22.9.168 Version/12.00", "Opera/12.0(Windows NT 5.1;U;en)Presto/22.9.168 Version/12.00", "Mozilla/5.0 (Windows NT 5.1) Gecko/20100101 Firefox/14.0 Opera/12.0", "Opera/9.80 (Windows NT 6.1; WOW64; U; pt) Presto/2.10.229 Version/11.62", "Opera/9.80 (Windows NT 6.0; U; pl) Presto/2.10.229 Version/11.62", "Opera/9.80 (Macintosh; Intel Mac OS X 10.6.8; U; fr) Presto/2.9.168 Version/11.52", "Opera/9.80 (Macintosh; Intel Mac OS X 10.6.8; U; de) Presto/2.9.168 Version/11.52", "Opera/9.80 (Windows NT 5.1; U; en) Presto/2.9.168 Version/11.51", "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; de) Opera 11.51", "Opera/9.80 (X11; Linux x86_64; U; fr) Presto/2.9.168 Version/11.50", "Opera/9.80 (X11; Linux i686; U; hu) Presto/2.9.168 Version/11.50", "Opera/9.80 (X11; Linux i686; U; ru) Presto/2.8.131 Version/11.11", "Opera/9.80 (X11; Linux i686; U; es-ES) Presto/2.8.131 Version/11.11", "Mozilla/5.0 (Windows NT 5.1; U; en; rv:1.8.1) Gecko/20061208 Firefox/5.0 Opera 11.11", "Opera/9.80 (X11; Linux x86_64; U; bg) Presto/2.8.131 Version/11.10", "Opera/9.80 (Windows NT 6.0; U; en) Presto/2.8.99 Version/11.10", "Opera/9.80 (Windows NT 5.1; U; zh-tw) Presto/2.8.131 Version/11.10", "Opera/9.80 (Windows NT 6.1; Opera Tablet/15165; U; en) Presto/2.8.149 Version/11.1", "Opera/9.80 (X11; Linux x86_64; U; Ubuntu/10.10 (maverick); pl) Presto/2.7.62 Version/11.01", "Opera/9.80 (X11; Linux i686; U; ja) Presto/2.7.62 Version/11.01", "Opera/9.80 (X11; Linux i686; U; fr) Presto/2.7.62 Version/11.01", "Opera/9.80 (Windows NT 6.1; U; zh-tw) Presto/2.7.62 Version/11.01", "Opera/9.80 (Windows NT 6.1; U; zh-cn) Presto/2.7.62 Version/11.01", "Opera/9.80 (Windows NT 6.1; U; sv) Presto/2.7.62 Version/11.01", "Opera/9.80 (Windows NT 6.1; U; en-US) Presto/2.7.62 Version/11.01", "Opera/9.80 (Windows NT 6.1; U; cs) Presto/2.7.62 Version/11.01", "Opera/9.80 (Windows NT 6.0; U; pl) Presto/2.7.62 Version/11.01", "Opera/9.80 (Windows NT 5.2; U; ru) Presto/2.7.62 Version/11.01", "Opera/9.80 (Windows NT 5.1; U;) Presto/2.7.62 Version/11.01", "Opera/9.80 (Windows NT 5.1; U; cs) Presto/2.7.62 Version/11.01", "Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US; rv:1.9.2.13) Gecko/20101213 Opera/9.80 (Windows NT 6.1; U; zh-tw) Presto/2.7.62 Version/11.01", "Mozilla/5.0 (Windows NT 6.1; U; nl; rv:1.9.1.6) Gecko/20091201 Firefox/3.5.6 Opera 11.01", "Mozilla/5.0 (Windows NT 6.1; U; de; rv:1.9.1.6) Gecko/20091201 Firefox/3.5.6 Opera 11.01", "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; de) Opera 11.01", "Opera/9.80 (X11; Linux x86_64; U; pl) Presto/2.7.62 Version/11.00", "Opera/9.80 (X11; Linux i686; U; it) Presto/2.7.62 Version/11.00", "Opera/9.80 (Windows NT 6.1; U; zh-cn) Presto/2.6.37 Version/11.00", "Opera/9.80 (Windows NT 6.1; U; pl) Presto/2.7.62 Version/11.00", "Opera/9.80 (Windows NT 6.1; U; ko) Presto/2.7.62 Version/11.00", "Opera/9.80 (Windows NT 6.1; U; fi) Presto/2.7.62 Version/11.00", "Opera/9.80 (Windows NT 6.1; U; en-GB) Presto/2.7.62 Version/11.00", "Opera/9.80 (Windows NT 6.1 x64; U; en) Presto/2.7.62 Version/11.00"]}, "randomize": {"0": "chrome", "1": "chrome", "2": "chrome", "3": "chrome", "4": "chrome", "5": "chrome", "6": "chrome", "7": "chrome", "8": "chrome", "9": "chrome", "10": "chrome", "11": "chrome", "12": "chrome", "13": "chrome", "14": "chrome", "15": "chrome", "16": "chrome", "17": "chrome", "18": "chrome", "19": "chrome", "20": "chrome", "21": "chrome", "22": "chrome", "23": "chrome", "24": "chrome", "25": "chrome", "26": "chrome", "27": "chrome", "28": "chrome", "29": "chrome", "30": "chrome", "31": "chrome", "32": "chrome", "33": "chrome", "34": "chrome", "35": "chrome", "36": "chrome", "37": "chrome", "38": "chrome", "39": "chrome", "40": "chrome", "41": "chrome", "42": "chrome", "43": "chrome", "44": "chrome", "45": "chrome", "46": "chrome", "47": "chrome", "48": "chrome", "49": "chrome", "50": "chrome", "51": "chrome", "52": "chrome", "53": "chrome", "54": "chrome", "55": "chrome", "56": "chrome", "57": "chrome", "58": "chrome", "59": "chrome", "60": "chrome", "61": "chrome", "62": "chrome", "63": "chrome", "64": "chrome", "65": "chrome", "66": "chrome", "67": "chrome", "68": "chrome", "69": "chrome", "70": "chrome", "71": "chrome", "72": "chrome", "73": "chrome", "74": "chrome", "75": "chrome", "76": "chrome", "77": "chrome", "78": "chrome", "79": "chrome", "80": "chrome", "81": "chrome", "82": "chrome", "83": "chrome", "84": "chrome", "85": "chrome", "86": "chrome", "87": "chrome", "88": "chrome", "89": "chrome", "90": "chrome", "91": "chrome", "92": "chrome", "93": "chrome", "94": "chrome", "95": "chrome", "96": "chrome", "97": "chrome", "98": "chrome", "99": "chrome", "100": "chrome", "101": "chrome", "102": "chrome", "103": "chrome", "104": "chrome", "105": "chrome", "106": "chrome", "107": "chrome", "108": "chrome", "109": "chrome", "110": "chrome", "111": "chrome", "112": "chrome", "113": "chrome", "114": "chrome", "115": "chrome", "116": "chrome", "117": "chrome", "118": "chrome", "119": "chrome", "120": "chrome", "121": "chrome", "122": "chrome", "123": "chrome", "124": "chrome", "125": "chrome", "126": "chrome", "127": "chrome", "128": "chrome", "129": "chrome", "130": "chrome", "131": "chrome", "132": "chrome", "133": "chrome", "134": "chrome", "135": "chrome", "136": "chrome", "137": "chrome", "138": "chrome", "139": "chrome", "140": "chrome", "141": "chrome", "142": "chrome", "143": "chrome", "144": "chrome", "145": "chrome", "146": "chrome", "147": "chrome", "148": "chrome", "149": "chrome", "150": "chrome", "151": "chrome", "152": "chrome", "153": "chrome", "154": "chrome", "155": "chrome", "156": "chrome", "157": "chrome", "158": "chrome", "159": "chrome", "160": "chrome", "161": "chrome", "162": "chrome", "163": "chrome", "164": "chrome", "165": "chrome", "166": "chrome", "167": "chrome", "168": "chrome", "169": "chrome", "170": "chrome", "171": "chrome", "172": "chrome", "173": "chrome", "174": "chrome", "175": "chrome", "176": "chrome", "177": "chrome", "178": "chrome", "179": "chrome", "180": "chrome", "181": "chrome", "182": "chrome", "183": "chrome", "184": "chrome", "185": "chrome", "186": "chrome", "187": "chrome", "188": "chrome", "189": "chrome", "190": "chrome", "191": "chrome", "192": "chrome", "193": "chrome", "194": "chrome", "195": "chrome", "196": "chrome", "197": "chrome", "198": "chrome", "199": "chrome", "200": "chrome", "201": "chrome", "202": "chrome", "203": "chrome", "204": "chrome", "205": "chrome", "206": "chrome", "207": "chrome", "208": "chrome", "209": "chrome", "210": "chrome", "211": "chrome", "212": "chrome", "213": "chrome", "214": "chrome", "215": "chrome", "216": "chrome", "217": "chrome", "218": "chrome", "219": "chrome", "220": "chrome", "221": "chrome", "222": "chrome", "223": "chrome", "224": "chrome", "225": "chrome", "226": "chrome", "227": "chrome", "228": "chrome", "229": "chrome", "230": "chrome", "231": "chrome", "232": "chrome", "233": "chrome", "234": "chrome", "235": "chrome", "236": "chrome", "237": "chrome", "238": "chrome", "239": "chrome", "240": "chrome", "241": "chrome", "242": "chrome", "243": "chrome", "244": "chrome", "245": "chrome", "246": "chrome", "247": "chrome", "248": "chrome", "249": "chrome", "250": "chrome", "251": "chrome", "252": "chrome", "253": "chrome", "254": "chrome", "255": "chrome", "256": "chrome", "257": "chrome", "258": "chrome", "259": "chrome", "260": "chrome", "261": "chrome", "262": "chrome", "263": "chrome", "264": "chrome", "265": "chrome", "266": "chrome", "267": "chrome", "268": "chrome", "269": "chrome", "270": "chrome", "271": "chrome", "272": "chrome", "273": "chrome", "274": "chrome", "275": "chrome", "276": "chrome", "277": "chrome", "278": "chrome", "279": "chrome", "280": "chrome", "281": "chrome", "282": "chrome", "283": "chrome", "284": "chrome", "285": "chrome", "286": "chrome", "287": "chrome", "288": "chrome", "289": "chrome", "290": "chrome", "291": "chrome", "292": "chrome", "293": "chrome", "294": "chrome", "295": "chrome", "296": "chrome", "297": "chrome", "298": "chrome", "299": "chrome", "300": "chrome", "301": "chrome", "302": "chrome", "303": "chrome", "304": "chrome", "305": "chrome", "306": "chrome", "307": "chrome", "308": "chrome", "309": "chrome", "310": "chrome", "311": "chrome", "312": "chrome", "313": "chrome", "314": "chrome", "315": "chrome", "316": "chrome", "317": "chrome", "318": "chrome", "319": "chrome", "320": "chrome", "321": "chrome", "322": "chrome", "323": "chrome", "324": "chrome", "325": "chrome", "326": "chrome", "327": "chrome", "328": "chrome", "329": "chrome", "330": "chrome", "331": "chrome", "332": "chrome", "333": "chrome", "334": "chrome", "335": "chrome", "336": "chrome", "337": "chrome", "338": "chrome", "339": "chrome", "340": "chrome", "341": "chrome", "342": "chrome", "343": "chrome", "344": "chrome", "345": "chrome", "346": "chrome", "347": "chrome", "348": "chrome", "349": "chrome", "350": "chrome", "351": "chrome", "352": "chrome", "353": "chrome", "354": "chrome", "355": "chrome", "356": "chrome", "357": "chrome", "358": "chrome", "359": "chrome", "360": "chrome", "361": "chrome", "362": "chrome", "363": "chrome", "364": "chrome", "365": "chrome", "366": "chrome", "367": "chrome", "368": "chrome", "369": "chrome", "370": "chrome", "371": "chrome", "372": "chrome", "373": "chrome", "374": "chrome", "375": "chrome", "376": "chrome", "377": "chrome", "378": "chrome", "379": "chrome", "380": "chrome", "381": "chrome", "382": "chrome", "383": "chrome", "384": "chrome", "385": "chrome", "386": "chrome", "387": "chrome", "388": "chrome", "389": "chrome", "390": "chrome", "391": "chrome", "392": "chrome", "393": "chrome", "394": "chrome", "395": "chrome", "396": "chrome", "397": "chrome", "398": "chrome", "399": "chrome", "400": "chrome", "401": "chrome", "402": "chrome", "403": "chrome", "404": "chrome", "405": "chrome", "406": "chrome", "407": "chrome", "408": "chrome", "409": "chrome", "410": "chrome", "411": "chrome", "412": "chrome", "413": "chrome", "414": "chrome", "415": "chrome", "416": "chrome", "417": "chrome", "418": "chrome", "419": "chrome", "420": "chrome", "421": "chrome", "422": "chrome", "423": "chrome", "424": "chrome", "425": "chrome", "426": "chrome", "427": "chrome", "428": "chrome", "429": "chrome", "430": "chrome", "431": "chrome", "432": "chrome", "433": "chrome", "434": "chrome", "435": "chrome", "436": "chrome", "437": "chrome", "438": "chrome", "439": "chrome", "440": "chrome", "441": "chrome", "442": "chrome", "443": "chrome", "444": "chrome", "445": "chrome", "446": "chrome", "447": "chrome", "448": "chrome", "449": "chrome", "450": "chrome", "451": "chrome", "452": "chrome", "453": "chrome", "454": "chrome", "455": "chrome", "456": "chrome", "457": "chrome", "458": "chrome", "459": "chrome", "460": "chrome", "461": "chrome", "462": "chrome", "463": "chrome", "464": "chrome", "465": "chrome", "466": "chrome", "467": "chrome", "468": "chrome", "469": "chrome", "470": "chrome", "471": "chrome", "472": "chrome", "473": "chrome", "474": "chrome", "475": "chrome", "476": "chrome", "477": "chrome", "478": "chrome", "479": "chrome", "480": "chrome", "481": "chrome", "482": "chrome", "483": "chrome", "484": "chrome", "485": "chrome", "486": "chrome", "487": "chrome", "488": "chrome", "489": "chrome", "490": "chrome", "491": "chrome", "492": "chrome", "493": "chrome", "494": "chrome", "495": "chrome", "496": "chrome", "497": "chrome", "498": "chrome", "499": "chrome", "500": "chrome", "501": "chrome", "502": "chrome", "503": "chrome", "504": "chrome", "505": "chrome", "506": "chrome", "507": "chrome", "508": "chrome", "509": "chrome", "510": "chrome", "511": "chrome", "512": "chrome", "513": "chrome", "514": "chrome", "515": "chrome", "516": "chrome", "517": "chrome", "518": "chrome", "519": "chrome", "520": "chrome", "521": "chrome", "522": "chrome", "523": "chrome", "524": "chrome", "525": "chrome", "526": "chrome", "527": "chrome", "528": "chrome", "529": "chrome", "530": "chrome", "531": "chrome", "532": "chrome", "533": "chrome", "534": "chrome", "535": "chrome", "536": "chrome", "537": "chrome", "538": "chrome", "539": "chrome", "540": "chrome", "541": "chrome", "542": "chrome", "543": "chrome", "544": "chrome", "545": "chrome", "546": "chrome", "547": "chrome", "548": "chrome", "549": "chrome", "550": "chrome", "551": "chrome", "552": "chrome", "553": "chrome", "554": "chrome", "555": "chrome", "556": "chrome", "557": "chrome", "558": "chrome", "559": "chrome", "560": "chrome", "561": "chrome", "562": "chrome", "563": "chrome", "564": "chrome", "565": "chrome", "566": "chrome", "567": "chrome", "568": "chrome", "569": "chrome", "570": "chrome", "571": "chrome", "572": "chrome", "573": "chrome", "574": "chrome", "575": "chrome", "576": "chrome", "577": "chrome", "578": "chrome", "579": "chrome", "580": "chrome", "581": "chrome", "582": "chrome", "583": "chrome", "584": "chrome", "585": "chrome", "586": "chrome", "587": "chrome", "588": "chrome", "589": "chrome", "590": "chrome", "591": "chrome", "592": "chrome", "593": "chrome", "594": "chrome", "595": "chrome", "596": "chrome", "597": "chrome", "598": "chrome", "599": "chrome", "600": "chrome", "601": "chrome", "602": "chrome", "603": "chrome", "604": "chrome", "605": "chrome", "606": "chrome", "607": "chrome", "608": "chrome", "609": "chrome", "610": "chrome", "611": "chrome", "612": "chrome", "613": "chrome", "614": "chrome", "615": "chrome", "616": "chrome", "617": "chrome", "618": "chrome", "619": "chrome", "620": "chrome", "621": "chrome", "622": "chrome", "623": "chrome", "624": "chrome", "625": "chrome", "626": "chrome", "627": "chrome", "628": "chrome", "629": "chrome", "630": "chrome", "631": "chrome", "632": "chrome", "633": "chrome", "634": "chrome", "635": "chrome", "636": "chrome", "637": "chrome", "638": "chrome", "639": "chrome", "640": "chrome", "641": "chrome", "642": "chrome", "643": "chrome", "644": "chrome", "645": "chrome", "646": "chrome", "647": "chrome", "648": "chrome", "649": "chrome", "650": "chrome", "651": "chrome", "652": "chrome", "653": "chrome", "654": "chrome", "655": "chrome", "656": "chrome", "657": "chrome", "658": "chrome", "659": "chrome", "660": "chrome", "661": "chrome", "662": "chrome", "663": "chrome", "664": "chrome", "665": "chrome", "666": "chrome", "667": "chrome", "668": "chrome", "669": "chrome", "670": "chrome", "671": "chrome", "672": "chrome", "673": "chrome", "674": "chrome", "675": "chrome", "676": "chrome", "677": "chrome", "678": "chrome", "679": "chrome", "680": "chrome", "681": "chrome", "682": "chrome", "683": "chrome", "684": "chrome", "685": "chrome", "686": "chrome", "687": "chrome", "688": "chrome", "689": "chrome", "690": "chrome", "691": "chrome", "692": "chrome", "693": "chrome", "694": "chrome", "695": "chrome", "696": "chrome", "697": "chrome", "698": "chrome", "699": "chrome", "700": "chrome", "701": "chrome", "702": "chrome", "703": "chrome", "704": "chrome", "705": "chrome", "706": "chrome", "707": "chrome", "708": "chrome", "709": "chrome", "710": "chrome", "711": "chrome", "712": "chrome", "713": "chrome", "714": "chrome", "715": "chrome", "716": "chrome", "717": "chrome", "718": "chrome", "719": "chrome", "720": "chrome", "721": "chrome", "722": "chrome", "723": "chrome", "724": "chrome", "725": "chrome", "726": "chrome", "727": "chrome", "728": "chrome", "729": "chrome", "730": "chrome", "731": "chrome", "732": "chrome", "733": "chrome", "734": "chrome", "735": "chrome", "736": "chrome", "737": "chrome", "738": "chrome", "739": "chrome", "740": "chrome", "741": "chrome", "742": "chrome", "743": "chrome", "744": "chrome", "745": "chrome", "746": "chrome", "747": "chrome", "748": "chrome", "749": "chrome", "750": "chrome", "751": "chrome", "752": "chrome", "753": "chrome", "754": "chrome", "755": "chrome", "756": "chrome", "757": "chrome", "758": "chrome", "759": "chrome", "760": "chrome", "761": "chrome", "762": "chrome", "763": "chrome", "764": "chrome", "765": "chrome", "766": "chrome", "767": "chrome", "768": "chrome", "769": "chrome", "770": "chrome", "771": "chrome", "772": "chrome", "773": "chrome", "774": "chrome", "775": "chrome", "776": "chrome", "777": "chrome", "778": "chrome", "779": "chrome", "780": "chrome", "781": "chrome", "782": "chrome", "783": "chrome", "784": "chrome", "785": "chrome", "786": "chrome", "787": "chrome", "788": "chrome", "789": "chrome", "790": "chrome", "791": "chrome", "792": "chrome", "793": "chrome", "794": "chrome", "795": "chrome", "796": "chrome", "797": "chrome", "798": "chrome", "799": "chrome", "800": "chrome", "801": "chrome", "802": "chrome", "803": "internetexplorer", "804": "internetexplorer", "805": "internetexplorer", "806": "internetexplorer", "807": "internetexplorer", "808": "internetexplorer", "809": "internetexplorer", "810": "internetexplorer", "811": "internetexplorer", "812": "internetexplorer", "813": "internetexplorer", "814": "internetexplorer", "815": "internetexplorer", "816": "internetexplorer", "817": "internetexplorer", "818": "internetexplorer", "819": "internetexplorer", "820": "internetexplorer", "821": "internetexplorer", "822": "internetexplorer", "823": "internetexplorer", "824": "internetexplorer", "825": "internetexplorer", "826": "internetexplorer", "827": "internetexplorer", "828": "internetexplorer", "829": "internetexplorer", "830": "internetexplorer", "831": "internetexplorer", "832": "internetexplorer", "833": "internetexplorer", "834": "internetexplorer", "835": "internetexplorer", "836": "internetexplorer", "837": "internetexplorer", "838": "internetexplorer", "839": "firefox", "840": "firefox", "841": "firefox", "842": "firefox", "843": "firefox", "844": "firefox", "845": "firefox", "846": "firefox", "847": "firefox", "848": "firefox", "849": "firefox", "850": "firefox", "851": "firefox", "852": "firefox", "853": "firefox", "854": "firefox", "855": "firefox", "856": "firefox", "857": "firefox", "858": "firefox", "859": "firefox", "860": "firefox", "861": "firefox", "862": "firefox", "863": "firefox", "864": "firefox", "865": "firefox", "866": "firefox", "867": "firefox", "868": "firefox", "869": "firefox", "870": "firefox", "871": "firefox", "872": "firefox", "873": "firefox", "874": "firefox", "875": "firefox", "876": "firefox", "877": "firefox", "878": "firefox", "879": "firefox", "880": "firefox", "881": "firefox", "882": "firefox", "883": "firefox", "884": "firefox", "885": "firefox", "886": "firefox", "887": "firefox", "888": "firefox", "889": "firefox", "890": "firefox", "891": "firefox", "892": "firefox", "893": "firefox", "894": "firefox", "895": "firefox", "896": "firefox", "897": "firefox", "898": "firefox", "899": "firefox", "900": "firefox", "901": "firefox", "902": "firefox", "903": "firefox", "904": "firefox", "905": "firefox", "906": "firefox", "907": "firefox", "908": "firefox", "909": "firefox", "910": "firefox", "911": "firefox", "912": "firefox", "913": "firefox", "914": "firefox", "915": "firefox", "916": "firefox", "917": "firefox", "918": "firefox", "919": "firefox", "920": "firefox", "921": "firefox", "922": "firefox", "923": "firefox", "924": "firefox", "925": "firefox", "926": "firefox", "927": "firefox", "928": "firefox", "929": "firefox", "930": "firefox", "931": "firefox", "932": "firefox", "933": "firefox", "934": "firefox", "935": "safari", "936": "safari", "937": "safari", "938": "safari", "939": "safari", "940": "safari", "941": "safari", "942": "safari", "943": "safari", "944": "safari", "945": "safari", "946": "safari", "947": "safari", "948": "safari", "949": "safari", "950": "safari", "951": "safari", "952": "safari", "953": "safari", "954": "safari", "955": "safari", "956": "safari", "957": "safari", "958": "safari", "959": "safari", "960": "safari", "961": "safari", "962": "safari", "963": "safari", "964": "safari", "965": "safari", "966": "safari", "967": "safari", "968": "opera", "969": "opera", "970": "opera", "971": "opera", "972": "opera", "973": "opera", "974": "opera", "975": "opera", "976": "opera", "977": "opera", "978": "opera", "979": "opera", "980": "opera", "981": "opera", "982": "opera", "983": "opera", "984": "opera"}}
```



### 6、扩展

##### 1、`**kwargs`:控制访问的参数，均为可选项

+ params: 字典或字节序列，作为参数增加到url中
+ data:字典，字节序列或文件对象,作为Request的内容
+ json: JSON格式的数据，作为Request的内容
+ headers: 字典, HTTP定制头
+ cookie: 字典或CooKiJar, Request中的cookie
+ auth: 元组，支持HTTP认证功能
+ files: 字典类型，传输文件
+ timeout: 设定超时时间，秒为单位
+ proxies [ˈprɑksiz] : 字典类型，设定访问代理服务器，可以增加登录认证
+ allow_redirects: True/False,默认为True,重定向开关
+ stream: True/False，默认为True,获取内容立即下载开关
+ verify[ˈverəti] : True/False默认Ture,认证ssl证书开关
+ cert  [sɜːrt]: 本地ssl证书路径

##### 2、参数详解

+ `params`

  字典或字节序列，作为参数增加到url中

  ```powershell
  payload = {'key1': 'one', 'key2': 'two'}
  r = requests.request('GET', 'http://python123.io/ws', params=payload)
  
  print(r.url)
  http://python123.io/ws?key1=one&key2=two
  ```

+ `data`

  字典，字节序列或文件对象,作为Request的内容

  ```python
  import requests
  payload = {'key1':'one', 'key2':'two'}
  url = 'http://httpbin.org/put'
  r = requests.put(url=url, data=payload)
  # or 
  r = requests.put(url=url, data='ABCDEFG') #字符串
  ```

+ `json`
  JSON格式的数据，作为Request的内容

  ```python
  kv = {'name': 'youdi', 'role': 'king', 'rank': 'the one'}
  url = 'http://httpbin.org/post'
  r = requests.request(method='POST', url=url, json=kv)
  print(r.text)
  {
    "args": {}, 
    "data": "{\"role\": \"king\", \"rank\": \"the one\", \"name\": \"youdi\"}",  #json格式，其实就是字符串
    "files": {}, 
    "form": {}, 
    "headers": {
      "Accept": "*/*", 
      "Accept-Encoding": "gzip, deflate", 
      "Connection": "close", 
      "Content-Length": "52", 
      "Content-Type": "application/json", 
      "Host": "httpbin.org", 
      "User-Agent": "python-requests/2.13.0"
    }, 
    "json": {
      "name": "youdi", 
      "rank": "the one", 
      "role": "king"
    }, 
    "origin": "183.60.175.16", 
    "url": "http://httpbin.org/post"
  }
  ```

+ `headers`
  字典, HTTP定制头部信息，隐藏爬虫信息，模拟浏览器的头部信息

  ```python
  url = 'http://httpbin.org/post'
  
  r = requests.request('POST', url)
  
  # 头部信息
  r.request.headers
  # 观察User-Agent
  {'Accept': '*/*', 'User-Agent': 'python-requests/2.13.0', 'Connection': 'keep-alive', 'Accept-Encoding': 'gzip, deflate', 'Content-Length': '0'}
  
  
  #加入headers后
  headers = { # 浏览器代理
      "User-Agent": "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.133 Safari/537.36"
  }
  r = requests.request('POST', url, headers=headers)
  
  print(r.request.headers)
  {'Accept': '*/*', 'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.133 Safari/537.36', 'Connection': 'keep-alive', 'Accept-Encoding': 'gzip, deflate', 'Content-Length': '0'}
  ```

+ `cookie`
  字典或CooKiJar, Request中的cookie

  ```python
  import requests
  import urllib.parse
  url = 'http://www.baidu.com'
  headers = {
      'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/15.0 Safari/605.1.15',
  
  }
  r = requests.request(method='GET', url=url, headers=headers)
  cookie = r.cookies
  print(r.request.headers)
  # print(r.text)
  print(cookie)
  print(requests.cookies.RequestsCookieJar)
  # 获取cookie字典
  cookiedict = requests.utils.dict_from_cookiejar(cookie)
  url = 'https://www.baidu.com/s?wd=%E7%BE%8E%E5%A5%B3&rsv_spt=1&rsv_iqid=0xfde6582b00129a7b&issp=1&f=8&rsv_bp=1&rsv_idx=2&ie=utf-8&tn=baiduhome_pg&rsv_enter=1&rsv_dl=ib&rsv_sug3=5&rsv_sug1=4&rsv_sug7=100&rsv_sug2=0&rsv_btype=i&inputT=965&rsv_sug4=965'.format(urllib.parse.quote('迪丽热巴'))
  print(url)
  r_baidu = requests.request('GET', url=url, headers=headers, cookies=cookie)
  r.encoding = 'gbk'
  print(r_baidu.content)
  with open('baidu.html', 'wb') as f:
      f.write(r_baidu.content)
  ```

+ `auth`
  元组，支持HTTP认证功能

  ```python
  import requests
  # 最简单的http验证
  from requests.auth import HTTPBasicAuth
  
  r = requests.get('http://httpbin.org/auth', auth=HTTPBasicAuth('user', 'user'))
  # r = requests.get('http://httpbin.org/auth', auth=('user', 'user'))
  print(r.status_code)
  ```

+ `files`
  字典类型，传输文件

  ```python
  fs = {file: open('data.xls', 'rb')}
  # 使用files参数就可以了
  r = requests.request('POST','http://httpbin.org/post',files=fs)
  ```

+ `timesout`
  设定超时时间，秒为单位

  ```python
  import requests
  from requests.exceptions import ReadTimeout
  
  try:
    # 设置必须在500ms内收到响应，不然或抛出ReadTimeout异常
    response = requests.get("http://httpbin.org/get", timeout=0.5)
             print(response.status_code)
  except ReadTimeout:
    print('Timeout')
  ```

+ `proxies`
  字典类型，设定访问代理服务器，可以增加登录认证

  ```python
  import requests
  
  #普通代理
  proxies = {
     "http": "http://127.0.0.1:1080",
     "https": "https://127.0.0.1:1080",
  }
  # 往请求中设置代理(proxies)
  r = requests.get("https://www.taobao.com", proxies=proxies)
  print(r.status_code)
  
  # 带有用户名和密码的代理
  proxies = {
     "http": "http://user:password@127.0.0.1:9743/",
  }
  r = requests.get("https://www.taobao.com", proxies=proxies)
  print(r.status_code)
  
  # 设置socks代理,翻墙必备
  proxies = {
     'http': 'socks5://127.0.0.1:1080',
     'https': 'socks5://127.0.0.1:1080'
  }
  r = requests.get("https://www.google.com", proxies=proxies)
  print(r.status_code)
  ```

+ `allow_redirects`
  True/False,默认为True,重定向开关

  ```python
  r = requests.request('GET','http://httpbin.org/get',allow_redirects=False)
  ```

+ `stream`
  True/False，默认为True,获取内容立即下载开关

  ```python
  r = requests.request('GET','http://httpbin.org/get/**.txt',stream=False)
  ```

+ `verity`
  True/False默认Ture,认证ssl证书开关

  ```python
  # 无证书访问
  r = requests.get('https://www.12306.cn')
  # 在请求https时，request会进行证书的验证，如果验证失败则会抛出异常
  print(r.status_code)
  
  
  # 关闭验证，但是仍然会报出证书警告
  r = requests.get('https://www.12306.cn',verify=False)
  print(r.status_code)
  
  # 消除关闭证书验证的警告
  from requests.packages import urllib3
  
  # 关闭警告
  urllib3.disable_warnings()
  r = requests.get('https://www.12306.cn',verify=False)
  print(r.status_code)
  ```

+ `cert`
  本地ssl证书路径

  证书下载方式  https://www.csdn.net/tags/NtzaYg5sMTM0NjMtYmxvZwO0O0OO0O0O.html

  ```python
  # 设置本地证书
  r = requests.get('https://www.12306.cn', cert=('/home/youdi/Download/**.crt', '/hone/youdi/.ssh/**.key'))
  print(r.status_code)
  ```

### 7、小结

1. requests.utils.dict_from_cookiejar能够实现cookiejar转化为字典
2. 请求方法中添加verify=False能够实现请求过程中不验证证书
3. 请求方法中添加timeout能够实现强制程序返回结果的能够，否则会报错
4. retrying模块能够实现捕获函数的异常，反复执行函数的效果，和timeout配合使用，能够解决网络波动带来的请求不成功的问题





