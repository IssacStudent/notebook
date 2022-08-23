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

起始行为HTTP版本，200和OK为状态码以及原因短语，下一行为创建响应的日期时间（属于首部字段），最后空一行，为资源实体的主体（在案例中为html形式的内容）

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

[^1]:对无状态的说明：HTTP协议自身不会对请求与响应做持久化处理，主要是为了更快处理**大量事务**
[^2]:在REST架构的网站中，PUT和POST都可以向HTTP服务器提交数据，但PUT一般是幂等的。若不存在则新建内容，若存在则修改内容，具体见[RFC文档](https://www.rfc-editor.org/rfc/rfc7231)。
