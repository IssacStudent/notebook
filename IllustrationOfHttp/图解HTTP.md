# 图解HTTP

[toc]

## Web以及网络基础

### 基本知识

HTTP（HyperText Transfer Protocol，超文本传输协议）

__客户端__借助该协议发请求到__服务端__，获取**信息**

TCP/IP协议族：

1.   应用层（FTP, DNS）
2.   传输层（TCP, UDP）
3.   网络层（IP）（路由，选择数据传输路线）
4.   数据链路层

### IP协议

**ARP协议**：将IP地址解析为MAC地址

**IP路由**：

![请求路由](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/1460000042226421)



### TCP协议

1.   将数据分割成报文段（segment），将每个报文段可靠地传输给对方。接收方再按照序号，以原来的顺序，重新组装请求报文。（一般报文是指HTTP请求报文）

2.   保证数据到达目标

     三次握手，SYN，ACK

### DNS服务

域名解析：域名--->DNS服务--->IP地址，或者返回来，通过IP反查域名

### 其他协议与HTTP的关系

<img src=https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/view height=700px/>

### URL与URL

URI：统一资源标识符，通过某个协议来表示资源的定位标识符

URL：统一资源定位符，表示资源的地点

**URL是URI的子集**

协议有很多种，如http、ftp、file、telnet等

#### 绝对URI的格式

以`http://user:pass@www.example.jp:80/dir/index.htm?uid=1#ch1`为例

| http:://         | user:pass        | www.example.jp                                    | 80                         | /dir/index.htm   | uid=1                          | ch1            |
| ---------------- | ---------------- | ------------------------------------------------- | -------------------------- | ---------------- | ------------------------------ | -------------- |
| 协议方案名       | 登录信息（认证） | 服务器地址                                        | 端口号                     | 带层次的文件路径 | 查询字符串                     | 片段标识符     |
| 协议方案名附冒号 | 认证信息（可选） | DNS可解析的域名/IPv4地址/用方括号括起来的IPv6地址 | 可选（自动使用默认端口号） | 定位资源的位置   | 查询字符串可以传入参数（可选） | 子资源（可选） |

## 简单的HTTP协议（以HTTP1.1为例）

### 请求与响应报文中的内容

**请求报文案例**

```http
POST /from/entry HTTP/1.1
Host: hackr.jp
Connection: keep-alive
Content-Type: application/x-www-form-urlencoded
Content-Length: 16

name=ueno&age=37
```

格式：POST为请求方法（method），之后为URI，HTTP版本协议号。

接下来为HTTP首部字段

最后为请求内容

**响应报文案例**

```http
HTTP/1.1 200 OK
Date: Tue, 10 Jul 2012 06:60:15 GMT
Content-Length: 362
Content-Type: text/html

<html>
......
```

起始行为HTTP版本，200和OK为状态码以及原因短语，下一行为创建响应的日期时间（属于首部字段），最后空一行，为资源实体的主体（在案例中为html形式的内容）。

### HTTP为无状态的协议（stateless）[^1]

HTTP协议自身不会保留之前一切请求或者响应报文的信息，如果需要保存用户的状态（如购物网站的登录状态、购物车、用户信息等），需要使用Cookie来管理状态。

### HTTP方法

*   GET：用来访问被URI识别的资源，如文本或CGI执行后的输出结果。、
*   POST：用来传输实体的主体。
*   PUT：用来传输文件[^2]。
*   HEAD：获得报文的首部信息，一般用来确认URI的有效性以及资源更新的日期时间等。
*   DELETE：删除文件，与PUT相反。
*   OPTIONS：查询针对请求的URI指定**资源支持的方法**。
*   TRACE：追踪路径。在首部Max-Forwards中填入数值，每经过一个服务器端就减去1，这样可以查看发出去的请求的路径。
*   CONNECT：建立隧道，使用隧道协议进行TCP通信。主要使用SSL（Secure Sockets Layer）和TLS（Transport Layer Security）协议来加密通信内容，经过网络隧道进行传输。
*   ~~LINK：建立和资源之间的联系（在HTTP1.1中被废弃）。~~
*   ~~UNLINK：断开连接关系（在HTTP1.1中被废弃）。~~

### HTTP持久连接

初始版本的HTTP协议，每进行一次就会建立/断开一次TCP连接，开销很大，因此在HTTP1.1版本开始，增加了持久连接的方法，只要任意一端没有明确提出断开连接，则**保持**TCP连接状态。

带来的好处：

1.   减轻服务器端负载
2.   加快页面响应与加载速度

在HTTP1.1中，所有连接**默认**是持久连接。

#### 管线化（pipelining）

持久连接使得请求可以管线化，从同步发送变为异步发送，可以一次性并行发送多个请求，比持久连接还快。

### 使用Cookie进行状态管理

服务端在进行响应的时候，**响应报文**中会有一个Set-Cookie的首部字段信息，有了这个信息之后，**客户端**会**保存**下来，下一次发送的时候会在**请求报文**中加入Cookie值。服务端收到这个Cookie值之后，会检查，从而判断这是哪一个客户端发来的请求，在服务器的记录（Session）上拿到之前客户端的状态信息。

因此：

1.   Cookie在客户端记录
2.   Session在服务端记录
3.   Cookie可以看作Session的唯一id
4.   Session会过期，因此隔一段时间会需要重新登录。以Tomcat服务器为例，过期时间默认为30分钟。

## HTTP报文内的信息

HTTP报文是多行数据构成的字符串文本，用**CR+LF**作为换行符，即0x0d0a，CR为Carriage Return，回车，LF为Line Feed，换行。报文首部和报文主体之间用CR+LF分隔。

### HTTP报文结构

包含请求行、状态行、首部。其中首部又包含请求首部、响应首部、通用首部、实体首部。

除此之外还有一些其他的（RFC中未定义的首部）信息，如Cookie。

### 编码提升传输速率

HTTP通信中的基本单位为**报文**，由8位组成字节流。

传输内容可以进行编码，使大小发生改变而所含信息量不发生变化。一般是服务端编码，客户端解码。

传输的数据可以进行分割，即分块传输编码（Chunked Transfer Coding），每一块都会使用十六进制标记块大小，最后一块使用0（CR+LF）。

### 多种数据的多部分对象集合

HTTP协议中采用了多部分对象集合（Multipart），即发送的报文中可以含有多种类型的实体（如文件上传）。

多部分对象集合包含的对象如下：

+   `multipart/form-data`，一般用于Web表单文件上传时使用。
+   `multipart/byteranges`，如果状态码为206（Partial Content，部分内容）时，表名响应报文包含了多个范围的内容。

要使用以上多部分对象集合，需要在首部字段中加上`Content-Type`。具体见[HTTP首部](#custom-id)。

多部分对象中的各类实体之间使用**boundary**来划分。例如：

```http
Content-Type: multipart/form-data; boundary=AaB03x
　--AaB03x
Content-Disposition: form-data; name="field1"
  
Joe Blow
--AaB03x
Content-Disposition: form-data; name="pics"; filename="file1.txt"
Content-Type: text/plain
　
...（file1.txt的数据）...
--AaB03x--
```

上述`multipart/form-data`报文中指定了分隔符为*"AaB03x"*，那么在多个实体之间需要通过"\-\-AaB03x"来划分，使用"\-\-AaB03x\-\-"标记作为结束。

```http
HTTP/1.1 206 Partial Content
Date: Fri, 13 Jul 2012 02:45:26 GMT
Last-Modified: Fri, 31 Aug 2007 02:02:20 GMT
Content-Type: multipart/byteranges; boundary=THIS_STRING_SEPARATES
 
--THIS_STRING_SEPARATES
Content-Type: application/pdf
Content-Range: bytes 500-999/8000
...（范围指定的数据）...
--THIS_STRING_SEPARATES
Content-Type: application/pdf
Content-Range: bytes 7000-7999/8000
...（范围指定的数据）...
--THIS_STRING_SEPARATES--
```

上述`multipart/byteranges`报文中指定了*"THIS_STRING_SEPARATES"*作为分隔符。

多部分对象集合的**每个**部分类型中，都可以含有首部字段，另外，某个部分中可以**嵌套**使用多部分集合。

### 获取部分内容的范围请求

对于大文件的上传，可以使用`Range`首部来指定字节范围。例如

*   `Range: bytes=5001-10000`
*   `Range: bytes=5001-`
*   `Range: bytes=0-3000, 5000-7000`[^3]

对于范围请求，响应会返回状态码为`206 ParticalContent`的响应报文。

如果服务器**无法**响应范围请求，会返回`200 OK`和**完整**的实体内容。

### 内容协商返回最合适的内容

同一个 Web 网站有可能存在着多份相同内容的页面。比如英语版和中文版的 Web 页面，它们内容上虽相同，但使用的语言却不同。当浏览器的默认语言为英语或中文，访问相同 URI 的 Web 页面时，则会显示对应的英语版或中文版的 Web 页面。这样的机制称为**内容协商（Content Negotiation）**。客户端与服务端就响应的内容（语言、字符集、编码方式等）进行交涉，之后返回最为适合的资源。例如使用以下首部：

+   Accept
+   Accept-Charset
+   Accept-Encoding
+   Accept-Language
+   Content-Language

内容协商有三大类型：

+   服务器驱动协商：服务器根据客户端（也就是浏览器）发送的信息进行判定。
+   客户端驱动协商：由客户端进行内容协商的方式。用户从浏览器显示的可选项列表中手动选择。还可以利用 JavaScript 脚本在 Web 页面上自动进行上述选择。比如按 OS 的类型或浏览器类型，自行切换成 PC 版页面或手机版页面。
+   透明协商：服务器驱动和客户端驱动的结合体。

## HTTP状态码

## 与HTTP协作的Web服务器

## HTTP首部 {#custom-id}

## HTTPS

## 认证机制

## 基于HTTP的功能追加协议

## 构建Web内容的技术

## Web的攻击技术

[^1]:对无状态的说明：HTTP协议自身不会对请求与响应做持久化处理，主要是为了更快处理**大量事务**
[^2]:在REST架构的网站中，PUT和POST都可以向HTTP服务器提交数据，但PUT一般是幂等的。若不存在则新建内容，若存在则修改内容，具体见[RFC文档](https://www.rfc-editor.org/rfc/rfc7231)。
[^3]:对于多重范围的请求，响应的首部字段会设置`Content-Type: multipart/byteranges`后返回响应报文。
