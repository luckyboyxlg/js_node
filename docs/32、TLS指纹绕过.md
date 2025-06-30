# 三十二、TLS指纹和JA3指纹绕过

## 前言

### **1、什么是TLS指纹校验?**

客户端   http    服务端
客户端     https = TLS/SSL + http   服务端
也就是说，TLS只存在于https请求中

**服务端接收请求后**：

​					第1次 TLS/SSL(握手通过/不通过)

​					 第2次 http(数据)

### **2、服务器如何识别？**

它通过分析客户端在 TLS 握手阶段发送的“特征指纹”，比如：

- 支持的加密套件（Cipher Suites）
- 扩展（Extensions）
- 压缩方法（Compression Methods）
- 协议版本（TLS Version）
- 顺序、长度等细节

这些信息组合起来可以形成一个唯一的“TLS 指纹”，服务器可以用它来识别请求来源是否为浏览器、requests、curl、Selenium、Playwright 等。

### **3、为什么 requests 被识别为爬虫？**

因为 `requests` 使用的是 Python 标准库中的底层 HTTPS 实现（基于 `urllib3` 和 `OpenSSL`），其默认 TLS 握手参数与主流浏览器（如 Chrome、Firefox）完全不同，所以它的 TLS 指纹非常容易被识别出来。

**视频资料**

+ https://www.bilibili.com/video/BV16u4y1j7eN?vd_source=87cd9028314be53c0d99d132ffe7796d&spm_id_from=333.788.player.switch&p=2
+ https://www.bilibili.com/video/BV1H94y1p7ME?vd_source=87cd9028314be53c0d99d132ffe7796d&spm_id_from=333.788.player.switch&p=3

## 一、常见指纹

### 1、JA3 指纹

**JA3** 是一种用于生成 TLS 客户端指纹的方法。它通过收集 TLS 握手过程中的几个关键字段来创建一个独特的指纹，这些字段包括：

- **SSL 版本**
- **接受的密码套件（Accepted Ciphers）**
- **扩展列表（Extensions）**
- **椭圆曲线（Elliptic Curves，如果适用）**
- **椭圆曲线格式（EC Point Formats，如果适用）**

这些信息被组合并进行哈希处理，生成一个固定的字符串，这个字符串就是 JA3 指纹。由于不同应用程序在建立TLS连接时会请求特定版本的协议、支持的加密算法等，这使得每个应用都有可能拥有独一无二的 JA3 指纹。因此，JA3 指纹可以帮助识别出特定的应用程序或客户端，即便它们使用了代理或其他手段试图隐藏自己的身份。

### 2、TLS指纹

术语“TLS 指纹”通常指的是更广泛的概念，它可以包含 JA3 以及其他基于 TLS 协议特性的指纹技术。TLS 指纹不仅限于客户端，也可以用于服务器。例如：

- **服务端的证书链**：可以通过分析服务器提供的证书链来识别服务器的身份。
- **TLS 实现细节**：如使用的具体 TLS 版本、支持的加密套件、TLS 扩展等。

TLS 指纹技术可以用来识别客户端和服务器的独特配置，有助于发现伪装流量、异常活动或是特定软件的行为模式。

### 3、区分 JA3 和 TLS 指纹

- **关注点不同**：JA3 主要专注于客户端的 TLS 握手特征，而 TLS 指纹则是一个更为宽泛的概念，既适用于客户端也适用于服务器。
- **数据来源不同**：JA3 使用的是客户端发起的 TLS 连接中的初始握手数据，而 TLS 指纹可能还会考虑整个会话期间的所有交互，包括但不限于证书交换、密钥协商等。
- **应用场景差异**：虽然两者都可以用来增强安全性，但 JA3 更多地用于检测和分类来自已知应用的流量，而 TLS 指纹的应用范围更广，可用于多种目的，比如验证服务器身份、监测潜在的安全威胁等。

总的来说，尽管 JA3 指纹是 TLS 指纹的一部分，但它特别针对的是客户端的行为特征，而 TLS 指纹涵盖的内容更加广泛，包括客户端和服务器双方的信息。理解这两者的区别有助于更好地利用它们进行网络安全监控和管理。

### 4、其它指纹

在网络流量分析和网络安全领域，除了JA3和TLS指纹之外，还有多种其他类型的指纹技术用于识别和跟踪网络连接中的客户端或服务器特征。这些指纹技术有助于更全面地了解网络活动的性质，识别潜在的安全威胁，并采取相应的措施。以下是一些常见的指纹类型：

+ **HTTP 指纹**

  - **描述**: HTTP指纹通过分析HTTP请求头（如User-Agent, Accept-Language, Accept-Encoding等）来识别客户端。

  - **用途**: 可以用来区分不同浏览器、操作系统以及应用程序版本。

+ **TCP/IP 指纹**

  - **描述**: TCP/IP指纹基于TCP协议的行为特征，如窗口大小、MSS（最大报文段长度）、TTL（生存时间）值等。

  - **用途**: 帮助识别操作系统类型及版本，因为不同的操作系统实现TCP/IP协议栈的方式有所不同。

+  **DNS 指纹**

  - **描述**: DNS指纹是通过分析DNS查询行为（如查询频率、响应模式等）来识别特定的应用程序或服务。

  - **用途**: 常用于发现恶意软件通信或其他异常网络行为。

+ **User-Agent 字符串**

  - **描述**: User-Agent字符串包含在HTTP请求头中，提供了关于发起请求的客户端信息，包括浏览器类型、版本、操作系统等。

  - **用途**: 广泛应用于网站优化、内容适配以及安全分析中。

+ **HTML Canvas 指纹**

  - **描述**: HTML Canvas指纹利用了HTML5 Canvas API生成图像时的不同渲染结果，由于不同设备和浏览器对Canvas的支持程度和渲染方式存在差异，因此可以形成独特的指纹。

  - **用途**: 主要用于追踪用户，尽管这种方法可能侵犯隐私，但在某些情况下被用于广告定向投放等。

+ **WebRTC 指纹**

  - **描述**: WebRTC是一种支持浏览器之间实时通信的技术，其指纹可以通过获取本地IP地址、候选端口范围等信息进行构建。

  - **用途**: 可用于增强用户识别能力，但也可能成为隐私泄露的风险点。

+ **字体指纹**

  - **描述**: 字体指纹基于系统上安装的字体列表及其属性（如名称、样式、大小等），每个系统的字体配置往往是独一无二的。

  - **用途**: 作为一种辅助手段用于提高用户识别精度。

+ **屏幕分辨率与色彩深度**

  - **描述**: 这些信息通常可以从JavaScript中读取，反映了用户的显示器设置情况。

  - **用途**: 结合其他指纹数据一起使用，以增加唯一性。

+ **插件和扩展指纹**

  - **描述**: 浏览器插件和扩展也可以作为指纹的一部分，因为它们的存在与否及其版本号能够提供额外的信息。

  - **用途**: 同样用于增强用户识别的准确性。

### 5、采集时如何区分是什么指纹？

在Python中进行HTTP请求时，识别当前连接的JA3或TLS指纹并不是直接通过代码实现的。JA3和TLS指纹实际上是外部观察者（如网络监控工具）基于客户端与服务器之间的TLS握手信息生成的指纹。这些指纹用于识别客户端的行为特征，例如使用的加密套件、协议版本等。

不过，如果你想要了解你的Python应用在发起HTTPS请求时可能产生的JA3指纹或者更广泛的TLS指纹特征，你可以采取以下几种方法：

### 方法 1: 使用 Wireshark 或类似的网络分析工具

Wireshark 是一个强大的网络协议分析工具，可以捕获并分析网络流量。你可以用它来捕获你的Python应用发出的TLS握手数据包，并手动计算JA3指纹或其他TLS指纹。

1. **安装 Wireshark**:
   - 根据你的操作系统下载并安装 Wireshark。
2. **启动 Wireshark 并开始捕获**:
   - 在 Wireshark 中选择正确的网络接口开始捕获数据包。
   - 运行你的Python脚本发起HTTPS请求。
   - 停止捕获后，在Wireshark中过滤出相关的TLS握手数据包（使用过滤器如 `tls`）。
3. **分析 TLS 握手信息**:
   - 查看捕获的数据包，找到Client Hello消息，提取其中的TLS版本、加密套件列表、扩展等信息。
   - 使用在线工具或自己编写脚本来根据 JA3 规则生成指纹。

### 方法 2: 使用 mitmproxy

`mitmproxy` 是一个支持拦截、修改和回放HTTP/HTTPS流量的交互式SSL/TLS代理。它可以用来查看和修改你的Python应用发出的所有请求，包括详细的TLS握手信息。

1. **安装 mitmproxy**:

   ```
   pip install mitmproxy
   ```

2. **启动 mitmproxy**:

   - 运行 `mitmproxy` 或 `mitmdump` 来启动代理服务器，默认监听在 `127.0.0.1:8080`。

3. **配置 Python 应用使用 mitmproxy**:

   - 修改你的Python应用以使用这个代理。例如，如果你使用`requests`库：

     ```python
     import requests
     proxies = {
         'http': 'http://127.0.0.1:8080',
         'https': 'http://127.0.0.1:8080',
     }
     response = requests.get('https://example.com', proxies=proxies)
     print(response.text)
     ```

4. **查看 TLS 握手信息**:

   - 在 `mitmproxy` 界面中，你可以看到所有经过代理的请求及其详细信息，包括TLS握手细节。

5. 代码

   showtls.py   用于打印指纹

   ```python
   from mitmproxy import tls
   import hashlib
   
   def ja3_fingerprint(tls_data):
       """
       生成 JA3 指纹
       :param tls_data: 包含 TLS Client Hello 数据的对象
       :return: JA3 指纹字符串
       """
       # 提取必要的字段
       tls_version = f"{tls_data.client_hello.version // 256}.{tls_data.client_hello.version % 256}"
       cipher_suites = "-".join([str(hex(c)) for c in tls_data.client_hello.cipher_suites])
       extensions = "-".join([str(ext.type) for ext in tls_data.client_hello.extensions])
   
       elliptic_curves = ""
       ec_point_formats = ""
   
       for ext in tls_data.client_hello.extensions:
           if ext.type == 10:  # supported_groups (elliptic curves)
               elliptic_curves = "-".join([str(group) for group in ext.supported_groups]) if ext.supported_groups else ""
           elif ext.type == 11:  # ec_point_formats
               ec_point_formats = "-".join([str(fmt) for fmt in ext.ec_point_formats]) if ext.ec_point_formats else ""
   
       # 组合成 JA3 字符串
       raw_ja3 = ",".join([
           tls_version,
           cipher_suites,
           extensions,
           elliptic_curves,
           ec_point_formats
       ])
   
       # 计算 MD5 哈希值
       ja3_hash = hashlib.md5(raw_ja3.encode()).hexdigest()
   
       return f"JA3: {raw_ja3} -> Hash: {ja3_hash}"
   
   # 处理 TLS 客户端握手事件
   class Events:
       def clienthello(self, data: tls.ClientHelloData):
           print(ja3_fingerprint(data))
   
   # 注册事件处理器
   addons = [
       Events()
   ]
   ```

   运行命令

    mitmdump -s showtls.py

   Main.py  运行请求的代码

   ```python
   import requests
   from urllib3.exceptions import InsecureRequestWarning
   import urllib3
   
   # 禁用不安全请求警告
   urllib3.disable_warnings(InsecureRequestWarning)
   
   proxies = {
       'http': 'http://127.0.0.1:8080',
       'https': 'http://127.0.0.1:8080',
   }
   
   response = requests.get('https://ascii2d.net/',headers={'user-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac os x 10_15_7) ApplewebKit/537.36 (KHTML, like Gecko)Chrome/96.0.4664.93 safari/537.36'}, proxies=proxies)
   print(response.text)
   ```

   

### 方法 3: 直接检查 SSL/TLS 设置

虽然不能直接“读取”你的应用的JA3或TLS指纹，但你可以检查并调整你的应用所使用的SSL/TLS设置，这将影响最终生成的指纹。

- 如果你使用`requests`库，可以通过自定义`Session`对象中的`ssl`

  配置来改变一些参数：

```python
import ssl
from requests.adapters import HTTPAdapter
from urllib3.poolmanager import PoolManager

class TlsAdapter(HTTPAdapter):
    def init_poolmanager(self, connections, maxsize, block=False):
        ctx = ssl.create_default_context()
        ctx.set_ciphers('DEFAULT:@SECLEVEL=1')  # 示例：更改默认的加密套件
        self.poolmanager = PoolManager(
            num_pools=connections,
            maxsize=maxsize,
            block=block,
            ssl_version=ssl.PROTOCOL_TLSv1_2,  # 可以选择特定的TLS版本
            ssl_context=ctx)

session = requests.session()
adapter = TlsAdapter()
session.mount('https://', adapter)

response = session.get('https://example.com')
print(response.content)
```

**总结**

直接从Python代码内部无法直接获取到JA3或TLS指纹，因为这些指纹是基于外部观察者对TLS握手过程的分析得出的。但是，通过上述方法，你可以间接地了解到你的应用程序在发起HTTPS请求时可能产生的TLS指纹特性。对于安全测试或调试目的，这种方法可以帮助你更好地理解如何调整你的应用以适应特定的安全需求。

## 二、TLS指纹和Ja3指纹绕过

### 1、案例

+ 网站：https://ascii2d.net/

**浏览器访问**

![image-20250218165307943](./imgs/32、TLS指纹绕过.assets/image-20250218165307943.png)



**代码访问**

```python
import requests
url = "https://ascii2d.net/"
res = requests.get(url,headers={'user-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac os x 10_15_7) ApplewebKit/537.36 (KHTML, like Gecko)Chrome/96.0.4664.93 safari/537.36'})
print(res.text)
```

**返回结果**

![image-20250218165442884](./imgs/32、TLS指纹绕过.assets/image-20250218165442884.png)





### 2、检测并返回指纹信息

**网址：**  https://tls.browserleaks.com/json

![image-20250218172334675](./imgs/32、TLS指纹绕过.assets/image-20250218172334675.png)

### 3、更细致分析TLS工具

#### (1) 网址和下载

**网址**：https://www.wireshark.org/download.html

如果想要更细致分析TLS、可以使用wireshark

![image-20250218172816692](./imgs/32、TLS指纹绕过.assets/image-20250218172816692.png)

#### (2) 抓网卡

打开wireshark，选择要抓包监测的网卡，选择你上网使用的那个网卡。

![image-20250218173200673](./imgs/32、TLS指纹绕过.assets/image-20250218173200673.png)

#### (3) 筛选IP

获取域名ip并筛选

![image-20250218173055279](./imgs/32、TLS指纹绕过.assets/image-20250218173055279.png)



#### (4) 查看代码/浏览器发送请求的TLS指纹

![image-20250218173618501](./imgs/32、TLS指纹绕过.assets/image-20250218173618501.png)

### 2、绕过TLS指纹

#### (1) 方式一 

urllib3解决（有的可能不过，因为还会检测别的值）

requests发送请求依赖于 -> urllib3  那么当前代码中生成指纹的位置

注意版本(如果你当前的版本搜不到、那么尝试降低版本安装以下版本在查找)

```
pip install urllib3==1.26.15
pip install urllib3==1.26.16
pip install urllib3=-2.0.7
```

![image-20250218173939972](./imgs/32、TLS指纹绕过.assets/image-20250218173939972.png)

```python
import requests
urllib3.util.ssl_.DEFAULT_CIPHERS =":".join([
# "ECDHE+AESGCM"
# "ECDHE+CHACHA20"
# "DHE+AESGCM"，
# "DHE+CHACHA20"
# "ECDH+AESGCM"
# "DH+AESGCM"
# "ECDH+AES"
"DH+AES",
"RSA+AESGCM",
"RSA+AES",
"!aNULL",
"!eNULL",
"!MD5",
"!DSS",
])
url = "https://tls.browserleaks.com/json"
res = requests.get(url,headers={'user-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac os x 10_15_7) ApplewebKit/537.36 (KHTML, like Gecko)Chrome/96.0.4664.93 safari/537.36'})
print(res.json())
```

**说明：**

注释掉一些内容，使当前的值发生更改，以绕过SSL指纹

#### (2) 方式二 

curl-cffi 解决（推荐）

**模块地址：** https://pypi.org/project/curl-cffi/#description

+ curl是一个可以发送网络请求的工具
+ curl-impersonate是一个基于curl基础上进行开发的一个工具，可以完美的模拟主流的浏览器
+ curl_cffi，是套壳curl-impersonate，让此工具可以更方便的应用在Python中。

**安装**

+ pip install curl-cffi

**使用**

```python
from curl_cffi import requests
# TLS检测网站
url = "https://ascii2d.net/"
res = requests.get(url,headers={'user-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac os x 10_15_7) ApplewebKit/537.36 (KHTML, like Gecko)Chrome/96.0.4664.93 safari/537.36'},impersonate="chrome101")
print(res.text)
```

#### (3) 使用 `tls-client`（重点推荐）

这是一个专门用于模拟浏览器 TLS 指纹的 Node.js / Python 兼容库：

安装Python-TLS-Client

```python
pip install tls-client
```

打开终端或命令提示符，执行以下命令来安装Python-TLS-Client库：

示例（Python 绑定）：

```python
import tls_client

session = tls_client.Session(
    client_identifier="chrome_103"  # 模拟 Chrome 103 的 TLS 指纹
)

response = session.get(
    "https://tls.peet.ws/api/all",
    headers={
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.0.0 Safari/537.36"
    }
)
print(response.json())
```

**特点：**

- 支持多种浏览器指纹（Chrome、Firefox、Edge 等）
- 支持会话保持、Cookie、Headers 设置
- 可以配合代理使用
- 性能优于 Selenium

#### (4) 使用crawl4ai

+ **github地址**

  https://github.com/unclecode/crawl4ai?tab=readme-ov-file

+ 文档

  https://docs.crawl4ai.com/advanced/hooks-auth/

****

+ **安装**(最好使用虚拟环境)

  ```python
  # Install the package
  pip install -U crawl4ai
  
  # For pre release versions
  pip install crawl4ai --pre
  
  # Run post-installation setup
  crawl4ai-setup
  
  # Verify your installation
  crawl4ai-doctor
  ```

+ 简单实用

  ```python
  import asyncio
  from crawl4ai import *
  
  async def main():
      async with AsyncWebCrawler() as crawler:
          result = await crawler.arun(
              url="https://ascii2d.net/",
          )
          print(result.html)
  
  if __name__ == "__main__":
      asyncio.run(main())
  ```

  

