# TCP/IP 5层模型

OSI七层模型。

## 1、物理层（Physical layer）

**即网线。**网线里传递的是0101的二进制数。

## 2、数据链路层（Data Link layer ）

底层传输的010010101001...这些二级制位怎么才能让它有意义呢？数据链路层就出现了！

**数据链路层可以解析这些01001的二进制数据。**如按8位一组，发送及接收都按照8位一组来划分。把数据分为head和data。

head包含：（固定18个字节）

- 发送者（源地址，6个字节）
- 接收者（目标地址，6个字节）
- 数据类型（6个字节）

data包含：（最短46字节，最长1500字节）

- 数据包的具体内容

上面的源地址和目标地址指的是 Mac地址，也称网卡地址。每块网卡出厂时都被烧录上一个实际上唯一的Mac地址。如果一台电脑没有网卡，它将无法通过有线的方式联网。要想连WIFI，也需要网卡，这种叫无线网卡。

<img src="https://upload-images.jianshu.io/upload_images/12474664-89defed53bdd2446.png?imageMogr2/auto-orient/strip|imageView2/2/w/718/format/webp" alt="img" style="zoom:50%;" />

| **对应的网络协议** | 作用         |
| ------------------ | ------------ |
| ARP                | 地址解析协议 |



## 3、网络层（Network layer）

数据链路层通信需要靠广播，广播出去以后，所有人都听得见，所有人都会拆开这个包，读发送者是谁，接收者是谁，只要接收者不是自己就丢弃掉。为了效率必须得分多个局域网，局域网内才能通过广播这种方式。那么**各个局域网之间的通信，就要靠网络层。**

每个局域网的负责人就是网关，网关即网络关口的意思。

Mac地址是用来标识你这个局域网的某个位置，IP地址是用来标识你在哪个局域网。

 IP地址+Mac地址 唯一标识了你在互联网中的位置。

| **对应的网络协议** | 作用     |
| ------------------ | -------- |
| IP（ipv4、ipv6）   | 网际协议 |
|                    |          |





## 4、传输层（Transport layer ）

传输层的由来：网络层的ip帮我们区分子网，以太网层的mac帮我们找到主机，然后大家使用的都是应用程序，你的电脑上可能同时开启qq，暴风影音，等多个应用程序，

那么我们通过ip和mac找到了一台特定的主机，如何标识这台主机上的应用程序，答案就是**端口**，端口即应用程序与网卡关联的编号。

**传输层功能：建立端口到端口的通信**

> 补充：端口范围0-65535，0-1023为系统占用端口

| **对应的网络协议** | 作用                               |
| ------------------ | ---------------------------------- |
| TCP                | 三次握手建立连接，四次挥手断开连接 |
| UDP                |                                    |

**（重要）该层协议：**

- 要发送的内容多，需要将发送的内容分成多个数据包发送。（TCP协议  丢包后会重传，考虑可靠传输 ）
- 要发送的内容少，一个数据包就能发送全部内容。（UDP协议  丢包后不重传，不考虑可靠传输的）



## 7、应用层（Application layer）

会话层，表示层，应用层。

应用层由来：用户使用的都是应用程序，均工作于应用层，互联网是开发的，大家都可以开发自己的应用程序，数据多种多样，必须规定好数据的组织形式 。

**应用层功能：规定应用程序的数据格式。**

| **对应的网络协议** | 作用           |
| ------------------ | -------------- |
| HTTP               | 超文本传输协议 |
| FTP                | 文件传输协议   |
| DNS                | 域名系统       |

例：TCP协议可以为各种各样的程序传递数据，比如Email、WWW、FTP等等。那么，必须有不同协议规定电子邮件、网页、FTP数据的格式，这些应用程序协议就构成了”应用层”。



## 重要的网络协议

### 传输层TCP和UDP

处于传输层，传输数据可以是文本，视频，图片等。但都是一堆二进制数。

#### TCP

TCP：基于连接（如打电话）

UDP：基于非连接（如寄信）

TCP通过三次握手、传输确认、四次挥手来保证可靠连接的。

三次握手是创建连接过程，三次握手指的是发送了三包数据。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202303141837897.png" alt="image-20230314182702425" style="zoom: 67%;" />

通俗：SYN是能否建立连接，回复SYN+ACK是同意连接，发送ACK包连接建立。这三个包称为三次握手。

- 第一次是A端向B端发送请求，如果是只有一次握手的话，A端不知道B端是不是收到了这个请求。
- 第二次是B端确认收到A端请求，如果只有两次的话，B端不确定A端是否收到了确认消息，这个确认消息有可能会在半路丢了。
- 第三次是A端确认收到了B的确认消息，A和B双方都是通的，然后AB就可以建立连接相互通信了。

TCP如何处理丢包问题、包传输时乱序问题？传输确认。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202303141837185.png" alt="image-20230314183314971" style="zoom:67%;" />

四次挥手进行连接关闭。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202305052015541.png" alt="image-20230314183834636" style="zoom:67%;" />

通俗：FIN发起连接关闭请求，回复ACK表示知道了进入关闭等待状态，此时服务端还能向客户端发送数据，当发送FIN表示服务器发送完毕，ACK发送后客户端进入超时等待时间，时间一到关闭连接。服务端收到了ACK后立即关闭。

tcp是[全双工通信](https://www.zhihu.com/search?q=全双工通信&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1114866844})，服务端和[客服端](https://www.zhihu.com/search?q=客服端&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1114866844})都能发送和接收数据。

tcp在断开连接时，需要服务端和客服端都确定对方将不再发送数据。

**第1次挥手**

由客户端向服务端发起，服务端收到信息后就能确定客户端已经停止发送数据。

**第2次挥手**

由[服务端](https://www.zhihu.com/search?q=服务端&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1114866844})向客户端发起，客户端收到消息后就能确定服务端已经知道客户端不会再发送数据。

**第3次握手**

由服务端向客户端发起，客户端收到消息后就能确定服务端已经停止发送数据。

**第4次挥手**

由客户端向服务端发起，服务端收到信息后就能确定客户端已经知道服务端不会再发送数据。

**为什么不是3次挥手**

在客服端第1次挥手时，服务端可能还在发送数据。

所以第2次挥手和第3次挥手不能合并。

**为什么第四次挥手后，客户端要等待一段时间才进入 CLOSED 状态？**

为了确保客户端发送了第四次Ack包。如果第四次Ack包丢包了，服务器长时间没收到ack确认包后，服务器会重发Fin包，这时候客户端还没有关闭可以重发Ack包，并刷新超时时间。

#### UDP

是无连接的，尽最大可能交付。支持一对一、一对多、多对一和多对多的交互通信。

![image-20230329165158990](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202303291651053.png)

### 应用层HTTP协议

### 数据链路层ARP



# HTTP协议

## 请求报文

- 请求行（包含了请求方法、URL、http协议版本）
- 请求头（包含多个key-value，如主机地址、浏览器类型等）
- 请求体

```yml
GET http://www.example.com/ HTTP/1.1 【请求行】
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8
Cache-Control: max-age=0
Host: www.example.com
If-Modified-Since: Thu, 17 Oct 2019 07:18:26 GMT
If-None-Match: "3147526947+gzip"
Proxy-Connection: keep-alive
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 xxx 【请求头】


param1=1&param2=2 【请求体】
```

请求头主要的内容：

1. User-Agent： 发起请求的用户代理（浏览器、应用程序）的标识。
2. Accept : 指定客户端能够接收的内容类型。MIME 类型的格式是"大类型/小类型"。
3. Accept-Encoding：指定客户端可以支持的web服务器返回内容压缩编码类型。
4. Accept-Language ：指定客户端浏览器用来展示返回信息所优先选择的语言。
5. **Content-Length（POST请求特有）：**请求体的长度，以字节为单位。
6. **X-Requested-With（Ajax请求特有）：**值通常是`XMLHttpRequest`，指示该请求是由XMLHttpRequest对象发起的AJAX请求。
7. **Content-Type：**指定请求的内容类型，如果是AJAX请求一般指定JSON类型，值为`application/json`。
8. **Cookie：** 包含客户端发送给服务器的Cookie信息。

## 响应报文

- 响应行（包含协议版本、状态码以及描述，最常见的是 200 OK 表示请求成功了）
- 响应头
- 响应体

```yml
HTTP/1.1 200 OK 【响应行】
Age: 529651
Cache-Control: max-age=604800
Connection: keep-alive
Content-Encoding: gzip
Content-Length: 648
Content-Type: text/html; charset=UTF-8
Date: Mon, 02 Nov 2020 17:53:39 GMT
Etag: "3147526947+ident+gzip"
Expires: Mon, 09 Nov 2020 17:53:39 GMT
Keep-Alive: timeout=4
Last-Modified: Thu, 17 Oct 2019 07:18:26 GMT
Proxy-Connection: keep-alive
Set-Cookie: session_id=abcd1234; Expires=Wed, 05 May 2023 12:00:00 GMT; Path=/
Server: ECS (sjc/16DF)
Vary: Accept-Encoding
X-Cache: HIT	【响应头】

<!doctype html>
<html>
<head>
    <title>Example Domain</title>
	// 省略... 
</body>
</html>		【响应体】

```

响应头主要的内容：

1. Content-Type： 指定响应主体的内容类型。
2. Content-Length： 指定响应主体的长度（字节）。
3. **Set-Cookie：** 用于在响应中设置Cookie（包含cookie的值、过期时间和有效路径）。
4. **Location：** 用于重定向请求的目标URL。
5. Expires： 指定响应的过期时间。
6. Server： 指定响应的服务器软件信息。



## HTTP状态码

**常见的：202-请求成功、301-重定向、404 - 请求的资源不存在、500 - 内部服务器错误**

### 1XX 信息

- **100 Continue** ：表明到目前为止都很正常，客户端可以继续发送请求或者忽略这个响应。

### 2XX 成功

- **200 OK**
- **204 No Content** ：请求已经成功处理，但是返回的响应报文不包含实体的主体部分。一般在只需要从客户端往服务器发送信息，而不需要返回数据时使用。
- **206 Partial Content** ：表示客户端进行了范围请求，响应报文包含由 Content-Range 指定范围的实体内容。

### 3XX 重定向

- **301 Moved Permanently** ：永久性重定向
- **302 Found** ：临时性重定向
- **303 See Other** ：和 302 有着相同的功能，但是 303 明确要求客户端应该采用 GET 方法获取资源。
- 注：虽然 HTTP 协议规定 301、302 状态下重定向时不允许把 POST 方法改成 GET 方法，但是大多数浏览器都会在 301、302 和 303 状态下的重定向把 POST 方法改成 GET 方法。
- **304 Not Modified** ：如果请求报文首部包含一些条件，例如：If-Match，If-Modified-Since，If-None-Match，If-Range，If-Unmodified-Since，如果不满足条件，则服务器会返回 304 状态码。
- **307 Temporary Redirect** ：临时重定向，与 302 的含义类似，但是 307 要求浏览器不会把重定向请求的 POST 方法改成 GET 方法。

### 4XX 客户端错误

- **400 Bad Request** ：请求报文中存在语法错误。
- **401 Unauthorized** ：该状态码表示发送的请求需要有认证信息（BASIC 认证、DIGEST 认证）。如果之前已进行过一次请求，则表示用户认证失败。
- **403 Forbidden** ：请求被拒绝。
- **404 Not Found**

错误400和错误404区别：

400：说明此请求还未执行controller层，原因大多是传递参数与接收参数中有类型不一致或字段不匹配造成的
404：页面未找到，查看请求地址是否正确

### 5XX 服务器错误

- **500 Internal Server Error** ：服务器正在执行请求时发生错误。
- **503 Service Unavailable** ：服务器暂时处于超负载或正在进行停机维护，现在无法处理请求。



## Cookie

### 创建过程

服务器发送的响应报文包含 Set-Cookie 首部字段，客户端得到响应报文后把 Cookie 内容保存到浏览器中。

```html
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry

[page content]
```

客户端之后对同一个服务器发送请求时，会从浏览器中取出 Cookie 信息并通过 Cookie 请求首部字段发送给服务器。

```html
GET /sample_page.html HTTP/1.1
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```

 **JavaScript创建**

浏览器通过 `document.cookie` 属性可创建新的 Cookie，也可通过该属性访问非 HttpOnly 标记的 Cookie。

```html
document.cookie = "yummy_cookie=choco";
document.cookie = "tasty_cookie=strawberry";
console.log(document.cookie);
```

### 浏览器禁止Cookie

如何继续保持登陆状态？

1. URL重写，就是把session id直接附加在URL路径的后面。

2. 表单隐藏字段。就是服务器渲染页面时会自动修改表单，添加一个隐藏字段，以便在表单提交时能够把session id传递回服务器。

```html
<form name="testform" action="/xxx">
    <input type="hidden" name="jsessionid" value="ByOK3vjFD75aPnr4">
</form>                                       
```



## HTTPS

HTTP 有以下安全性问题：

- 使用明文进行通信，内容可能会被窃听；
- 不验证通信方的身份，通信方的身份有可能遭遇伪装；
- 无法证明报文的完整性，报文有可能遭篡改。

HTTPS 是让 HTTP 先和 SSL（Secure Sockets Layer）通信，再由 SSL 和 TCP 通信，也就是说 HTTPS 使用了隧道进行通信。

SSL加密协议：

- 身份验证。查看服务器CA证书，用于证明服务器的真实性和合法性。
- 加密。公钥加密（非对称加密）传输对称加密的密钥，再使用对称加密加密传输的数据。
- 数据完整性。SSL使用消息认证码（Message Authentication Code，MAC）通过对传输的数据应用散列函数和密钥生成的校验值。

缺点：

- HTTPS速度会更慢；
- 服务器需要支付证书授权的高额费用；



## GET和POST区别

#### GET

`GET`方法请求一个指定资源的表示形式，使用GET的请求应该只被用于获取数据

#### POST

`POST`方法用于将实体提交到指定的资源。

本质上都是`TCP`链接，并无差别。

区别：

- **数据传输方式：**GET查询数据是通过向URL后添加变量，没有请求体。而POST提交数据是放在请求体。所以导致GET是不安全的，数据被放在请求的 URL 中；Get 传输的数据量小，这主要是因为受 URL 长度限制；
- **缓存：**GET请求通常可以被浏览器缓存，发送相同的GET请求，浏览器可以直接从缓存中获取响应结果。POST请求通常不被浏览器缓存，每次发送POST请求浏览器都会向服务器请求最新的响应结果。
- **幂等性：**GET请求是幂等的，多次发送相同的GET请求获取数据不会对服务器端数据产生变化。而POST请求通常不是幂等的，多次发送相同的POST请求可能会导致服务器上的数据多次提交或产生重复的副作用。

**POST、PUT、DELETE请求区别**

从底层实现的角度来看，POST、PUT和DELETE请求方法在HTTP协议中的处理方式可以是相似的。这些请求方法的设计目的是为了标识和语义的区分，以便在应用层面进行不同的操作。



## 网络编程Socket



## 操作系统



## LInux