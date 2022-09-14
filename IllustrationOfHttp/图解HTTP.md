# 图解HTTP

[toc]

## Web以及网络基础

### 基本知识

HTTP（HyperText Transfer Protocol，超文本传输协议）

**客户端**借助该协议发请求到**服务端**，获取**信息**

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

<img alt = "与HTTP相关的协议" src=https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/view height=700px/>

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

## 简单的HTTP协议（以HTTP/1.1为例）

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
*   ~~LINK：建立和资源之间的联系（在HTTP/1.1中被废弃）。~~
*   ~~UNLINK：断开连接关系（在HTTP/1.1中被废弃）。~~

### HTTP持久连接

初始版本的HTTP协议，每进行一次就会建立/断开一次TCP连接，开销很大，因此在HTTP/1.1版本开始，增加了持久连接的方法，只要任意一端没有明确提出断开连接，则**保持**TCP连接状态。

带来的好处：

1.   减轻服务器端负载
2.   加快页面响应与加载速度

在HTTP/1.1中，所有连接**默认**是持久连接。

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

要使用以上多部分对象集合，需要在首部字段中加上`Content-Type`。具体见[HTTP首部](#HTTP首部)。

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

HTTP状态码：表示客户端发出的HTTP请求的返回结果、标记服务器端的处理是否正常、通知出现的错误等工作。

>   需要说明的是，状态码和真实的状态不一定是匹配的。比如Web应用程序内部发生错误，但是状态码依然可以返回200 OK。

状态码的类别如下：

| 状态码格式 | 类别                                 | 原因短语                   |
| ---------- | ------------------------------------ | -------------------------- |
| 1XX        | Information（信息性状态码）          | 接受的请求**正在处理**     |
| 2XX        | Success（成功状态码）                | 请求**正常**处理完毕       |
| 3XX        | Redirection（重定向状态码）          | 需要进行附加操作以完成请求 |
| 4XX        | Client Error（**客户端**错误状态码） | 服务器无法处理客户端的请求 |
| 5XX        | Server Error（**服务器**错误状态码） | 服务器处理请求出错         |

常见的状态码有14种。

### 2XX 成功

| 状态码              | 含义                                                         |
| ------------------- | ------------------------------------------------------------ |
| 200 OK              | 表示客户端的请求在服务器被正常处理了。                       |
| 204 No Content      | 表示请求处理成功，但是没有资源可以返回                       |
| 206 Partial Content | 表示客户端进行了**范围请求**，响应报文中有`Content-Type`信息 |

### 3XX 重定向

表示客户端需要进一步进行其他处理，才可以完成请求。

| 状态码                 | 含义                                                         |
| ---------------------- | ------------------------------------------------------------ |
| 301 Moved Permanently  | 永久性重定向，表示该资源已经被分配了新的URI。这时候，首部字段Location会提示新的URI，这个时候需要浏览器更新新的URI书签引用。 |
| 302 Found              | 临时性重定向，表示资源已被分配了新的URI，本次使用新的URI，但是这个资源不是永久移动，而是**临时**性质的。 |
| 303 See Other[^4][^5]  | 表示资源存在另一个URI，需要用**GET**方法重定向。             |
| 304 Not Modified       | 客户端发送附带条件的请求时[^6]，服务器资源没有改变，可以直接使用客户端**未过期**的缓存。例如发送`If-Modified-Since`头部，如果在特定时间之后，服务器并没有改变资源，那么就可以返回304，如果服务器对资源进行了修改，那么返回200 OK，并返回修改后的资源。 |
| 307 Temporary Redirect | 临时重定向，与302含义相同。307要求客户端不改变原先的请求方法。 |

### 4XX 客户端错误

表示客户端是发生错误的原因所在。

| 状态码           | 含义                                                         |
| ---------------- | ------------------------------------------------------------ |
| 400 Bad Request  | 表示请求报文中存在语法错误，需要修改请求内容后重新发请求。   |
| 401 Unauthorized | 1.表示请求需要有通过HTTP认证的信息。2.如果之前有过一次请求，则表示认证**失败**。 |
| 403 Forbidden    | 表示请求资源的访问未授权，被服务器拒绝了，服务器也没有必要给出详细的理由（当然写在响应体里也可以）。 |
| 404 Not Found    | 表示服务器上无法找到请求的资源。或是拒绝请求并且不愿说明理由时使用。 |

### 5XX 服务器错误

| 状态码                    | 含义                                                         |
| ------------------------- | ------------------------------------------------------------ |
| 500 Internal Server Error | 表示服务器在执行请求时出现了错误，可能是BUG或者是临时的故障。 |
| 503 Service Unavaliable   | 表示服务器正处于**超负载**或者**停机**维护阶段，无法处理请求。可以在首部字段`Retry-After`信息中告诉客户端接触以上状况需要的时间。 |

### 状态码表

更多HTTP状态码以及含义，可参考[附录](./Appendix.md#HTTP状态码表)。

## 与HTTP协作的Web服务器

### 虚拟主机管理多个域名

>   一台物理主机可以使用虚拟化技术，运行多个域名的服务。当域名通过DNS解析为IP时，多个域名对应同一个IP，为了清除究竟访问哪个域名服务，就需要在**`Host`**首部字段完整**指定主机名**或者**域名**的URI。

### 转发组件：代理、网关、隧道

*   代理：一种具有转发功能的应用程序。接手客户端的请求，发送给服务器；或是接收服务端返回的响应，转发给客户端。
*   网关：接收从客户端发送来的请求时，就像自己拥有资源的源服务器一样，对请求进行处理，比如转发给特定的服务器。有时候客户端并不知道自己的通信目标是一个网关。
*   隧道：客户端与服务器相隔甚远时，负责双方通信连接的应用程序。

#### 代理

代理服务器可以在客户端与源服务器之间进行转发，可以级联多台代理服务器，在转发时需要附加`Via`首部字段。

![多级代理服务器](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/image-20220831221310769.png)

使用代理可以：

1.   利用缓存技术减少网络带宽的流量。
2.   组织内部针对特定网站可以进行访问控制。
3.   获取访问日志。

缓存代理：资源直接从缓存服务器那里获取。

透明代理：对报文不做任何加工，成为透明代理（Transparent Proxy），否则就是非透明代理。

#### 网关

网关可以为通信线路上的服务提供**非HTTP协议**服务，如连接数据库，RPC等。

![网关](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/image-20220831222401853.png)

#### 隧道

可以使用SSL等加密手段进行通信。

### 缓存

缓存服务器可以节省通信流量与时间，在转发响应时，缓存服务器会留一份资源副本，客户端第二次请求的资源如果已经被缓存，则会向源服务器**<u>确认缓存资源的有效性</u>**，如果有效，直接返回给客户端。

## HTTP首部

本书主要涉及HTTP/1.1常用的首部字段。首部字段可以为服务器或者浏览器提供诸如报文大小、语言版本、认证信息等内容。

首部字段格式：

`首部字段名: 字段值[, 字段值2, ......]`

HTTP首部字段有以下几种类型：

1.   [通用首部字段](#通用首部字段)
2.   [请求首部字段](#请求首部字段)
3.   [响应首部字段](#响应首部字段)
4.   [实体首部字段](#实体首部字段)

HTTP/1.1规范了47种首部字段，见[附录](./Appendix.md#HTTP首部字段)。

还有一些不属于HTTP/1.1，但是也很常用的首部字段，如Cookie、Set-Cookie、Content-Disposition等。

### 通用首部字段

#### Cache-Control

一般用于操作缓存的工作机制。一般用法为

```http
Cache-Control: private, max-age=0, no-cache
```

即多个指令之间通过逗号分隔。可选指令有以下几种：

**缓存请求指令**

| 指令            | 参数   | 说明                           |
| --------------- | ------ | ------------------------------ |
| no-cache        | 无     | 强制向源服务器再次验证         |
| no-store        | 无     | 不缓存请求或者响应的任何内容   |
| max-age=[秒]    | 必需   | 响应的最大Age                  |
| max-stale=[秒]  | 可省略 | 接受已过期的响应               |
| min-fresh=[秒]  | 必需   | 期望在指定时间内的响应仍然有效 |
| no-transform    | 无     | 代理不可改变媒体类型           |
| only-if-cached  | 无     | 从缓存获取资源                 |
| cache-extension | -      | 新指令标记（token）            |

**缓存响应指令**

| 指令             | 参数   | 说明                                           |
| ---------------- | ------ | ---------------------------------------------- |
| no-cache         | 可省略 | 缓存前必须先确认其有效性                       |
| no-store         | 无     | 不缓存请求或者响应的任何内容                   |
| max-age=[秒]     | 必需   | 响应的最大Age值                                |
| s-maxage=[秒]    | 必需   | 公共缓存服务器响应的最大Age值                  |
| public           | 无     | 可向任意方提供响应的缓存                       |
| private          | 可省略 | 仅向特定用户返回响应                           |
| no-transform     | 无     | 代理不可更改媒体类型                           |
| must-revalidate  | 无     | 可缓存但是必须向源服务器进行确认               |
| proxy-revalidate | 无     | 要求中间缓存服务器对缓存的响应有效性再进行确认 |
| cache-extension  | -      | 新指令标记（token）                            |

*   是否可以缓存的指令
    *   public：公共缓存，其他用户也可以使用
    *   private：响应只以特定的用户作为对象。（缓存服务器只会对特定用户进行资源缓存）
    *   no-cache：使用no-cache的目的是为了避免从缓存中拿到**<u>过期</u>**的资源。
        *   对于客户端来说，表示客户端不会接受缓存的响应，请求必须由缓存服务器转发给源服务器。
        *   对于服务器返回的响应来说，表示缓存服务器禁止对资源进行缓存，并且源服务器以后也不会对缓存服务器提出的**资源有效性**进行确认。

*   控制可执行缓存到的对象的指令
    *   no-store：表示不能在本地存储请求或者响应的任意部分[^7]。

*   指定缓存期限与认证的指令

    *   max-age：

        *   对于客户端来说，如果缓存时间比指定的数值小，那就将缓存资源返回给客户端。max-age为0时，缓存服务器会直接转发请求给源服务器。
        *   对于服务器返回的响应来说，max-age表示资源**保存的最长时间**。

    *   s-maxage：与max-age相同，但是一般只适用于供多位用户使用的公共缓存服务器。

        >HTTP/1.1版本的服务器，处理相关字段的优先级为：s-maxage > max-age > Expires（首部字段）
        
    * min-fresh：要求缓存服务器返回至少还未过指定时间的缓存资源。
    
    * max-stale：表示接收缓存资源，即使过期。
    
        >   如果指令未指定参数值，那么无论经过多久，客户端都会接收响应；如果指令中指定了具体数值，那么即使过期，只要仍处于max-stale指定的时间内，仍旧会被客户端接收。
    
    * only-if-cached：表示客户端仅在缓存服务器本地缓存了目标资源的情况下才会返回。也就是说，该指令要求缓存服务器不重新加载响应，也不确认资源有效性。如果缓存服务器本地无响应，则返回504 Gateway Timeout。
    
    * must-revalidate：表示要向源服务器重新验证资源的有效性，否则返回504。另外，该指令会忽略max-stale。
    
    * proxy-revalidate：要求**所有的**缓存服务器都必须再次验证资源的有效性。
    
    * no-transform：缓存不能改变主体的媒体类型。这样可以防止缓存或者代理压缩图片等操作。


*   Cache-Control扩展

    cache-extension标记可以扩展首部字段的指令。格式为
    
    ```http
    `token [ "=" ( token | quoted-string ) ]`
    ```
    
    如：`Cache-Control: private, community="UCI"`。在这个首部信息中，private是Cache-Control这个首部字段有的指令，但是community不是，它只有缓存服务器可以理解，如果理解不了，就会忽略，因此这个字段是双方约定的。

#### Warning

该首部会通知用户一些与缓存相关问题的警告。格式为

```http
Warning: [警告码][警告主机:端口号] "[警告内容]" ([日期时间])
```

HTTP/1.1定义的警告码：

| 警告码 | 警告内容                         | 说明                                      |
| ------ | -------------------------------- | ----------------------------------------- |
| 110    | Response is stale                | 响应已过期                                |
| 111    | Revalidation failed              | 代理再次验证资源有效性时失败              |
| 112    | Disconnection operation          | 代理与互联网连接被故意切断                |
| 113    | Heuristic expiration             | 响应的使用期限超过24小时                  |
| 199    | Miscellaneous warning            | 任意的警告内容                            |
| 214    | Transformation applied           | 内容编码或者媒体类型被代理进行了转换/处理 |
| 299    | Miscellaneous persistent warning | 任意的警告内容（持久的）                  |


#### Connection

该字段有两个作用：

*   控制代理不再转发的字段

例如，`Connection: Upgrade`，那么代理服务器在进行下一次转发时，会先删除Upgrade这个字段。

*   管理持久连接

HTTP/1.1版本默认为**持久连接**。如果想要断开连接，需要设置`Connection: close`。

对于旧版本的HTTP，默认连接不是持久的，如果在旧版本HTTP协议上维持持续连接，需要指定`Connection: Keep-Alive`。服务器还会返回Keep-Alive首部字段，如图：

![image-20220914214456524](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/image-20220914214456524.png)

#### Date

该字段表名HTTP报文创建的日期和时间。

旧版本格式（RFC850）：`Date: Tue, 03 Jul 2012 04:40:59 GMT`

HTTP/1.1格式（RFC1123）：`Date: Tue, 03-Jul-12 04:40:59 GMT`

其他格式（与C标准库的asctime()函数格式一致）：`Date: Tue Jul 03 04:40:59 2012`

#### Pragma

如果客户端不接受缓存，则会设置`Pragma: no-cache`，一般还会设置`Cache-Control: no-cache`，以达到兼容的目的。

#### Trailer

该字段会事先说明，在**报文主体的后面**记录了哪些字段。

#### Transfer-Encoding

该字段规定了传输报文主体时才用的编码方式，且只对**分块传输编码**有效。

例如，指定`Transfer-Encoding: chunked`，之后的报文格式为

```http
cf0（十六进制为3312）
...3312字节的分块数据...
392（十六进制为914）
...914字节的分块数据...
```

#### Upgrade

该首部用来指定一个完全不同的通信协议，可以来检测HTTP协议是否可以使用更高版本进行通信。服务器可以返回101 Switching Protocols状态码，作为响应返回。一般情况下，该字段还需要设置首部字段`Connection: Upgrade`。如图：

![image-20220914221038303](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/image-20220914221038303.png)

#### Via

Via字段可以追踪客户端与服务器之间的传输路径，还可以**防止请求回环**的发生。如图：

![image-20220914221220714](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/image-20220914221220714.png)

### 请求首部字段



### 响应首部字段

### 实体首部字段

### 与Cookie相关的首部字段

### 其他首部字段

## 确保Web安全的HTTPS

### HTTP的缺点

### HTTPS=HTTP+加密+认证+完整性保护

## 认证机制

## 基于HTTP的功能追加协议

## 构建Web内容的技术

## Web的攻击技术

[^1]:对无状态的说明：HTTP协议自身不会对请求与响应做持久化处理，主要是为了更快处理**大量事务**
[^2]:在REST架构的网站中，PUT和POST都可以向HTTP服务器提交数据，但PUT一般是幂等的。若不存在则新建内容，若存在则修改内容，具体见[RFC文档](https://www.rfc-editor.org/rfc/rfc7231)。
[^3]:对于多重范围的请求，响应的首部字段会设置`Content-Type: multipart/byteranges`后返回响应报文。
[^4]:303与302类似，但是303状态码明确表示客户端应当用**GET方法**获取资源。

[^5]:301、302、303响应状态码返回时，几乎所有浏览器都会把POST改成GET，之后再次发送请求，但实际上，301和302是禁止这么做的。
[^6]:比如首部信息包含：`If-Match`、`If-Modified-Since`、`If-None-Match`、`If-Range`、`If-Unmodified-Since`
[^7]:注意区别：no-cache必须做强制性的有效性校验，而no-store侧重于真正的不存储任何资源。
