# 图解HTTP

[toc]

## Web 以及网络基础

### 基本知识

HTTP（HyperText Transfer Protocol，超文本传输协议）

**客户端**借助该协议发请求到**服务端**，获取**信息**

TCP/IP 协议族：

1.   应用层（FTP, DNS）
2.   传输层（TCP, UDP）
3.   网络层（IP）（路由，选择数据传输路线）
4.   数据链路层

### IP 协议

**ARP 协议**：将IP地址解析为 MAC 地址

**IP 路由**：

![请求路由](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/1460000042226421)



### TCP 协议

1.   将数据分割成报文段（segment），将每个报文段可靠地传输给对方。接收方再按照序号，以原来的顺序，重新组装请求报文。（一般报文是指 HTTP 请求报文）

2.   保证数据到达目标

     三次握手，SYN，ACK

### DNS 服务

域名解析：域名 ---> DNS 服务 ---> IP 地址，或者返回来，通过 IP 反查域名

### 其他协议与 HTTP 的关系

<img alt = "与 HTTP 相关的协议" src=https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/view height=700px/>

### URL 与 URL

URI：统一资源标识符，通过某个协议来表示资源的定位标识符

URL：统一资源定位符，表示资源的地点

**URL 是 URI 的子集**

协议有很多种，如 http、ftp、file、telnet 等

#### 绝对 URI 的格式

以 `http://user:pass@www.example.jp:80/dir/index.htm?uid=1#ch1` 为例

| http:://         | user:pass        | www.example.jp                                         | 80                         | /dir/index.htm   | uid=1                          | ch1            |
| ---------------- | ---------------- | ------------------------------------------------------ | -------------------------- | ---------------- | ------------------------------ | -------------- |
| 协议方案名       | 登录信息（认证） | 服务器地址                                             | 端口号                     | 带层次的文件路径 | 查询字符串                     | 片段标识符     |
| 协议方案名附冒号 | 认证信息（可选） | DNS 可解析的域名/ IPv4 地址/用方括号括起来的 IPv6 地址 | 可选（自动使用默认端口号） | 定位资源的位置   | 查询字符串可以传入参数（可选） | 子资源（可选） |

## 简单的 HTTP 协议（以 HTTP/1.1 为例）

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

格式：POST 为请求方法（method），之后为 URI，HTTP 版本协议号。

接下来为 HTTP 首部字段

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

起始行为 HTTP 版本，200 和 OK 为状态码以及原因短语，下一行为创建响应的日期时间（属于首部字段），最后空一行，为资源实体的主体（在案例中为 html 形式的内容）。

### HTTP 为无状态的协议（stateless）[^1]

HTTP 协议自身不会保留之前一切请求或者响应报文的信息，如果需要保存用户的状态（如购物网站的登录状态、购物车、用户信息等），需要使用 Cookie 来管理状态。

### HTTP 方法

*   GET：用来访问被 URI 识别的资源，如文本或 CGI 执行后的输出结果。、
*   POST：用来传输实体的主体。
*   PUT：用来传输文件[^2]。
*   HEAD：获得报文的首部信息，一般用来确认 URI 的有效性以及资源更新的日期时间等。
*   DELETE：删除文件，与 PUT 相反。
*   OPTIONS：查询针对请求的 URI 指定**资源支持的方法**。
*   TRACE：追踪路径。在首部 Max-Forwards 中填入数值，每经过一个服务器端就减去1，这样可以查看发出去的请求的路径。
*   CONNECT：建立隧道，使用隧道协议进行 TCP 通信。主要使用 SSL（Secure Sockets Layer）和 TLS（Transport Layer Security）协议来加密通信内容，经过网络隧道进行传输。
*   ~~LINK：建立和资源之间的联系（在 HTTP/1.1 中被废弃）。~~
*   ~~UNLINK：断开连接关系（在 HTTP/1.1 中被废弃）。~~

### HTTP 持久连接

初始版本的 HTTP 协议，每进行一次就会建立/断开一次 TCP 连接，开销很大，因此在 HTTP/1.1 版本开始，增加了持久连接的方法，只要任意一端没有明确提出断开连接，则**保持 **TCP 连接状态。

带来的好处：

1.   减轻服务器端负载
2.   加快页面响应与加载速度

在 HTTP/1.1 中，所有连接**默认**是持久连接。

#### 管线化（pipelining）

持久连接使得请求可以管线化，从同步发送变为异步发送，可以一次性并行发送多个请求，比持久连接还快。

### 使用 Cookie 进行状态管理

服务端在进行响应的时候，**响应报文**中会有一个 Set-Cookie 的首部字段信息，有了这个信息之后，**客户端**会**保存**下来，下一次发送的时候会在**请求报文**中加入 Cookie 值。服务端收到这个 Cookie 值之后，会检查，从而判断这是哪一个客户端发来的请求，在服务器的记录（Session）上拿到之前客户端的状态信息。

因此：

1.   Cookie 在客户端记录
2.   Session 在服务端记录
3.   Cookie 可以看作 Session 的唯一 id
4.   Session 会过期，因此隔一段时间会需要重新登录。以 Tomcat 服务器为例，过期时间默认为 30 分钟。

## HTTP 报文内的信息

HTTP 报文是多行数据构成的字符串文本，用 **CR+LF** 作为换行符，即 0x0d0a，CR 为 Carriage Return，回车，LF 为 Line Feed，换行。报文首部和报文主体之间用 CR+LF 分隔。

### HTTP 报文结构

包含请求行、状态行、首部。其中首部又包含请求首部、响应首部、通用首部、实体首部。

除此之外还有一些其他的（RFC 中未定义的首部）信息，如 Cookie。

### 编码提升传输速率

HTTP 通信中的基本单位为**报文**，由8位组成字节流。

传输内容可以进行编码，使大小发生改变而所含信息量不发生变化。一般是服务端编码，客户端解码。

传输的数据可以进行分割，即分块传输编码（Chunked Transfer Coding），每一块都会使用十六进制标记块大小，最后一块使用0（CR+LF）。

### 多种数据的多部分对象集合

HTTP 协议中采用了多部分对象集合（Multipart），即发送的报文中可以含有多种类型的实体（如文件上传）。

多部分对象集合包含的对象如下：

+   `multipart/form-data`，一般用于Web表单文件上传时使用。
+   `multipart/byteranges`，如果状态码为 206（Partial Content，部分内容）时，表明响应报文包含了多个范围的内容。

要使用以上多部分对象集合，需要在首部字段中加上 `Content-Type`。具体见 [HTTP 首部](#HTTP 首部)。

多部分对象中的各类实体之间使用 **boundary** 来划分。例如：

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

上述 `multipart/form-data` 报文中指定了分隔符为 *"AaB03x"*，那么在多个实体之间需要通过 "\-\-AaB03x" 来划分，使用 "\-\-AaB03x\-\-" 标记作为结束。

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

上述 `multipart/byteranges` 报文中指定了 *"THIS_STRING_SEPARATES"* 作为分隔符。

多部分对象集合的**每个**部分类型中，都可以含有首部字段，另外，某个部分中可以**嵌套**使用多部分集合。

### 获取部分内容的范围请求

对于大文件的上传，可以使用 `Range` 首部来指定字节范围。例如

*   `Range: bytes=5001-10000`
*   `Range: bytes=5001-`
*   `Range: bytes=0-3000, 5000-7000`[^3]

对于范围请求，响应会返回状态码为 `206 ParticalContent` 的响应报文。

如果服务器**无法**响应范围请求，会返回 `200 OK` 和**完整**的实体内容。

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

## HTTP 状态码

HTTP 状态码：表示客户端发出的 HTTP 请求的返回结果、标记服务器端的处理是否正常、通知出现的错误等工作。

>   需要说明的是，状态码和真实的状态不一定是匹配的。比如 Web 应用程序内部发生错误，但是状态码依然可以返回 200 OK。

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
| 206 Partial Content | 表示客户端进行了**范围请求**，响应报文中有 `Content-Type` 信息 |

### 3XX 重定向

表示客户端需要进一步进行其他处理，才可以完成请求。

| 状态码                 | 含义                                                         |
| ---------------------- | ------------------------------------------------------------ |
| 301 Moved Permanently  | 永久性重定向，表示该资源已经被分配了新的 URI。这时候，首部字段 Location 会提示新的 URI，这个时候需要浏览器更新新的 URI 书签引用。 |
| 302 Found              | 临时性重定向，表示资源已被分配了新的 URI，本次使用新的 URI，但是这个资源不是永久移动，而是**临时**性质的。 |
| 303 See Other[^4][^5]  | 表示资源存在另一个 URI，需要用 **GET** 方法重定向。          |
| 304 Not Modified       | 客户端发送[附带条件的请求](# If-Match)时[^6]，服务器资源没有改变，可以直接使用客户端**未过期**的缓存。例如发送 `If-Modified-Since` 头部，如果在特定时间之后，服务器并没有改变资源，那么就可以返回 304，如果服务器对资源进行了修改，那么返回 200 OK，并返回修改后的资源。 |
| 307 Temporary Redirect | 临时重定向，与 302 含义相同。307 要求客户端不改变原先的请求方法。 |

### 4XX 客户端错误

表示客户端是发生错误的原因所在。

| 状态码           | 含义                                                         |
| ---------------- | ------------------------------------------------------------ |
| 400 Bad Request  | 表示请求报文中存在语法错误，需要修改请求内容后重新发请求。   |
| 401 Unauthorized | 1.表示请求需要有通过 HTTP 认证的信息。2.如果之前有过一次请求，则表示认证**失败**。 |
| 403 Forbidden    | 表示请求资源的访问未授权，被服务器拒绝了，服务器也没有必要给出详细的理由（当然写在响应体里也可以）。 |
| 404 Not Found    | 表示服务器上无法找到请求的资源。或是拒绝请求并且不愿说明理由时使用。 |

### 5XX 服务器错误

| 状态码                    | 含义                                                         |
| ------------------------- | ------------------------------------------------------------ |
| 500 Internal Server Error | 表示服务器在执行请求时出现了错误，可能是 BUG 或者是临时的故障。 |
| 503 Service Unavaliable   | 表示服务器正处于**超负载**或者**停机**维护阶段，无法处理请求。可以在首部字段 `Retry-After` 信息中告诉客户端接触以上状况需要的时间。 |

### 状态码表

更多 HTTP 状态码以及含义，可参考[附录](./Appendix.md#HTTP 状态码表)。

## 与 HTTP 协作的 Web 服务器

### 虚拟主机管理多个域名

>   一台物理主机可以使用虚拟化技术，运行多个域名的服务。当域名通过 DNS 解析为 IP 时，多个域名对应同一个 IP，为了清除究竟访问哪个域名服务，就需要在 **`Host`** 首部字段完整**指定主机名**或者**域名**的 URI。

### 转发组件：代理、网关、隧道

*   代理：一种具有转发功能的应用程序。接手客户端的请求，发送给服务器；或是接收服务端返回的响应，转发给客户端。
*   网关：接收从客户端发送来的请求时，就像自己拥有资源的源服务器一样，对请求进行处理，比如转发给特定的服务器。有时候客户端并不知道自己的通信目标是一个网关。
*   隧道：客户端与服务器相隔甚远时，负责双方通信连接的应用程序。

#### 代理

代理服务器可以在客户端与源服务器之间进行转发，可以级联多台代理服务器，在转发时需要附加 `Via` 首部字段。

![多级代理服务器](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/image-20220831221310769.png)

使用代理可以：

1.   利用缓存技术减少网络带宽的流量。
2.   组织内部针对特定网站可以进行访问控制。
3.   获取访问日志。

缓存代理：资源直接从缓存服务器那里获取。

透明代理：对报文不做任何加工，成为透明代理（Transparent Proxy），否则就是非透明代理。

#### 网关

网关可以为通信线路上的服务提供**非 HTTP 协议**服务，如连接数据库，RPC 等。

![网关](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/image-20220831222401853.png)

#### 隧道

可以使用 SSL 等加密手段进行通信。

### 缓存

缓存服务器可以节省通信流量与时间，在转发响应时，缓存服务器会留一份资源副本，客户端第二次请求的资源如果已经被缓存，则会向源服务器**<u>确认缓存资源的有效性</u>**，如果有效，直接返回给客户端。

## HTTP 首部

本书主要涉及 HTTP/1.1 常用的首部字段。首部字段可以为服务器或者浏览器提供诸如报文大小、语言版本、认证信息等内容。

首部字段格式：

`首部字段名: 字段值[, 字段值2, ......]`

HTTP 首部字段有以下几种类型：

1.   [通用首部字段](#通用首部字段)
2.   [请求首部字段](#请求首部字段)
3.   [响应首部字段](#响应首部字段)
4.   [实体首部字段](#实体首部字段)

HTTP/1.1 规范了 47 种首部字段，见[附录](./Appendix.md#HTTP首部字段)。

还有一些不属于 HTTP/1.1，但是也很常用的首部字段，如 Cookie、Set-Cookie、Content-Disposition 等。

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
| max-age=[秒]    | 必需   | 响应的最大 Age                 |
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
| max-age=[秒]     | 必需   | 响应的最大 Age 值                              |
| s-maxage=[秒]    | 必需   | 公共缓存服务器响应的最大 Age 值                |
| public           | 无     | 可向任意方提供响应的缓存                       |
| private          | 可省略 | 仅向特定用户返回响应                           |
| no-transform     | 无     | 代理不可更改媒体类型                           |
| must-revalidate  | 无     | 可缓存但是必须向源服务器进行确认               |
| proxy-revalidate | 无     | 要求中间缓存服务器对缓存的响应有效性再进行确认 |
| cache-extension  | -      | 新指令标记（token）                            |

*   是否可以缓存的指令

    *   public：公共缓存，其他用户也可以使用
    *   private：响应只以特定的用户作为对象。（缓存服务器只会对特定用户进行资源缓存）
    *   no-cache：使用 no-cache 的目的是为了避免从缓存中拿到**<u>过期</u>**的资源。
        *   对于客户端来说，表示客户端不会接受缓存的响应，请求必须由缓存服务器转发给源服务器。
        *   对于服务器返回的响应来说，表示缓存服务器禁止对资源进行缓存，并且源服务器以后也不会对缓存服务器提出的**资源有效性**进行确认。

*   控制可执行缓存到的对象的指令

    *   no-store：表示不能在本地存储请求或者响应的任意部分[^7]。

*   指定缓存期限与认证的指令

    *   max-age：

        *   对于客户端来说，如果缓存时间比指定的数值小，那就将缓存资源返回给客户端。max-age 为 0 时，缓存服务器会直接转发请求给源服务器。
        *   对于服务器返回的响应来说，max-age 表示资源**保存的最长时间**。

    *   s-maxage：与 max-age 相同，但是一般只适用于供多位用户使用的公共缓存服务器。

        >HTTP/1.1 版本的服务器，处理相关字段的优先级为：s-maxage > max-age > Expires（首部字段）

    * min-fresh：要求缓存服务器返回至少还未过指定时间的缓存资源。

    * max-stale：表示接收缓存资源，即使过期。

        >   如果指令未指定参数值，那么无论经过多久，客户端都会接收响应；如果指令中指定了具体数值，那么即使过期，只要仍处于 max-stale 指定的时间内，仍旧会被客户端接收。

    * only-if-cached：表示客户端仅在缓存服务器本地缓存了目标资源的情况下才会返回。也就是说，该指令要求缓存服务器不重新加载响应，也不确认资源有效性。如果缓存服务器本地无响应，则返回 504 Gateway Timeout。

    * must-revalidate：表示要向源服务器重新验证资源的有效性，否则返回 504。另外，该指令会忽略 max-stale。

    * proxy-revalidate：要求**所有的**缓存服务器都必须再次验证资源的有效性。

    * no-transform：缓存不能改变主体的媒体类型。这样可以防止缓存或者代理压缩图片等操作。


*   Cache-Control 扩展

    cache-extension 标记可以扩展首部字段的指令。格式为

    ```http
    `token [ "=" ( token | quoted-string ) ]`
    ```

    如：`Cache-Control: private, community="UCI"`。在这个首部信息中，private 是 Cache-Control 这个首部字段有的指令，但是 community 不是，它只有缓存服务器可以理解，如果理解不了，就会忽略，因此这个字段是双方约定的。

#### Warning

该首部会通知用户一些与缓存相关问题的警告。格式为

```http
Warning: [警告码][警告主机:端口号] "[警告内容]" ([日期时间])
```

HTTP/1.1 定义的警告码：

| 警告码 | 警告内容                         | 说明                                      |
| ------ | -------------------------------- | ----------------------------------------- |
| 110    | Response is stale                | 响应已过期                                |
| 111    | Revalidation failed              | 代理再次验证资源有效性时失败              |
| 112    | Disconnection operation          | 代理与互联网连接被故意切断                |
| 113    | Heuristic expiration             | 响应的使用期限超过 24 小时                |
| 199    | Miscellaneous warning            | 任意的警告内容                            |
| 214    | Transformation applied           | 内容编码或者媒体类型被代理进行了转换/处理 |
| 299    | Miscellaneous persistent warning | 任意的警告内容（持久的）                  |


#### Connection

该字段有两个作用：

*   控制代理不再转发的字段

例如，`Connection: Upgrade`，那么代理服务器在进行下一次转发时，会先删除 Upgrade 这个字段。

*   管理持久连接

HTTP/1.1 版本默认为**持久连接**。如果想要断开连接，需要设置 `Connection: close`。

对于旧版本的 HTTP，默认连接不是持久的，如果在旧版本 HTTP 协议上维持持续连接，需要指定 `Connection: Keep-Alive`。服务器还会返回 Keep-Alive 首部字段，如图：

![image-20220914214456524](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/image-20220914214456524.png)

#### Date

该字段表明 HTTP 报文创建的日期和时间。

旧版本格式（RFC850）：`Date: Tue, 03 Jul 2012 04:40:59 GMT`

HTTP/1.1 格式（RFC1123）：`Date: Tue, 03-Jul-12 04:40:59 GMT`

其他格式（与 C 标准库的 asctime() 函数格式一致）：`Date: Tue Jul 03 04:40:59 2012`

#### Pragma

如果客户端不接受缓存，则会设置 `Pragma: no-cache`，一般还会设置 `Cache-Control: no-cache`，以达到兼容的目的。

#### Trailer

该字段会事先说明，在**报文主体的后面**记录了哪些字段。

#### Transfer-Encoding

该字段规定了传输报文主体时才用的编码方式，且只对**分块传输编码**有效。

例如，指定 `Transfer-Encoding: chunked`，之后的报文格式为

```http
cf0（十六进制为3312）
...3312字节的分块数据...
392（十六进制为914）
...914字节的分块数据...
```

#### Upgrade

该首部用来指定一个完全不同的通信协议，可以来检测 HTTP 协议是否可以使用更高版本进行通信。服务器可以返回 101 Switching Protocols 状态码，作为响应返回。一般情况下，该字段还需要设置首部字段 `Connection: Upgrade`。如图：

![image-20220914221038303](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/image-20220914221038303.png)

#### Via

Via字段可以追踪客户端与服务器之间的传输路径，还可以**防止请求回环**的发生。如图：

![image-20220914221220714](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/image-20220914221220714.png)

### 请求首部字段

#### Accept

该首部字段可以让客户端通知服务器，用户代理能够处理的媒体类型以及相对的优先级。媒体类型使用 type/subtype 格式，优先级使用 q= 来表示权重，范围为 0~1，不指定权重则为 1。

类型与权值之间用分号（;）进行分隔，类型之间用逗号（,）分隔，因此服务器提供多种内容时，优先返回权重值最高的媒体类型。

例如：

```http
Accept: text/html; q=0.3, text/html
```

因此，如果服务器有 html 格式和 text 格式的资源时，由于 text/html 的权重为 1，因此会返回 html 格式的资源。

#### Accept-Cahrset

该字段用来通知服务器用户代理支持的字符集以及字符集的优先顺序，同样可以指定多个字符集，并赋予权重值。

例如：

```http
Accept-Charst: iso-8859-5, unicode-1-1;q=0.8
```

#### Accept-Encoding

该字段用于通知服务器用户代理支持的内容编码以及优先级顺序。内容编码方式有如下几个：

*   gzip
*   compress
*   deflate
*   identity（不执行压缩或者不会变化的默认编码格式）

#### Accept-Language

可以指定语言集，例如：

```http
Accept-Language: zh-cn,zh;q=0.7,en-us,en;q=0.3
```

#### Authorization

该字段可以告知服务器，用户代理的认证信息。一般情况下，当需要认证时，服务器会返回 401 Unauthorized，之后的请求需要提供这个首部字段。如图：

![image-20220919214026233](../../../AppData/Roaming/Typora/typora-user-images/image-20220919214026233.png)

#### Expect

该字段用户告知服务器，期望出现的某种行为。例如：

```http
Expect: 100-continue
```

服务器无法理解时，会返回状态码 417-Expectation Failed。

#### From

该字段包含用户的电子邮件地址。一般用来显示搜索引擎等用户代理的负责人的电子邮件。

#### Host

若单台服务器分配了多个域名，那么需要指定 Host 这个首部字段来说明使用哪个域名对应的服务处理该请求。如果服务器未设定主机名，可以发送一个空值。

#### If-Match

类似于 If-xxx 这种样式的请求首部字段，都称之为条件请求，服务器判断该条件为真时，才会执行这个请求。

另外，对于某个特定资源，会有关联的**[实体标记（ETag）](#ETag)**值，当资源更新时，这个值也会随之更新。

服务器会比对字段值和实际资源的 ETag 值，当两者一致时，才会执行请求，否则会返回 412 Precondition Failed 的响应。

还可以使用星号（*），在这种情况下，服务器会**忽略** ETag 值。

![image-20220919221407217](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/image-20220919221407217.png)

#### If-None-Match

与 [If-Match](#If-Match) 作用相反。可以使用该首部信息来获取**最新资源**。

#### If-Modified-Since

当资源在指定时间之后发生过更新，则会返回资源。如果在指定时间之后，并没有发生资源的更新，那么服务器会返回 304 Not Modified 响应。

该字段可以用来确认代理或者客户端拥有的**本地资源**的**有效性**。资源的更新时间可以通过 [Last-Modified](#Last-Modified) 字段来确定。

![image-20220919221425178](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/image-20220919221425178.png)

#### If-Unmodified-Since

与 [If-Modified-Since](#If-Modified-Since) 相反，如果在指定时间之后没有发生修改，则处理请求，否则返回 412 Precondition Failed 响应。

#### If-Range

该字段包含的 [ETag](#ETag) 值或者时间与服务器资源一致时，才会处理客户端的范围请求。否则返回全体资源。

![image-20220919221809362](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/image-20220919221809362.png)

如果不适用If-Range请求头，如果客户端想要更新部分资源，那么需要发送 [If-Match](#If-Match) 和 [Range](#Range) 这两个请求头，但是服务器中，客户端所需要的**部分资源**已经失效了，因此会返回 412，此时，客户端需要再发一次请求，才可以获取新的。因此，使用 If-Range 请求头节省了时间。

#### Max-Forwards

该字段内容为十进制整数，表示可经过的服务器最大的数目。在请求前往下一个服务器进行转发之前，当前服务器会将该值减 1 后重新赋值。当某个服务器接收到 Max-Forwards 为 0 时，不再进行转发，而是直接返回响应。

在实际使用 HTTP 协议进行通信时，我们无法得知代理服务器的运行情况，例如：由于未知原因导致请求失败，或是导致请求陷入循环。这时我们就必须指定这个字段值，可以对传输路径的通信状况有所把握。

#### Proxy-Authorization

该字段与 [Authorization](#Authorization) 类似，都是进行认证的，但不同之处在于，该字段用于客户端与**代理**之间的认证行为。

#### Range

该字段可以指定范围请求。例如

```http
Range: bytes=5001-10000
```

服务器如果可以处理部分请求，则会返回部分请求的内容以及 206 Partial Content 响应，若无法处理部分请求，则会返回 200 OK 的响应以及全部资源。

#### Referer

首部字段 Referer 会告知服务器请求资源的原始 URI。

#### TE

该字段告知服务器客户端能够处理得传输编码方式以及优先级。与 [Accept-Encoding](#Accept-Encoding) 类似，但是TE用来表示**传输**编码，而 [Accept-Encoding](#Accept-Encoding) 用来表示**内容**编码。

#### User-Agent

该字段会记录创建请求的**浏览器**和**用户代理名称**等信息传达给服务器。

### 响应首部字段

#### Accept-Ranges

服务器使用该字段告诉客户端是否可以处理范围请求。可以处理则为 bytes，否则为 none。

#### Age

服务器使用该字段表示其在多久之前创建了响应。单位为秒。

如果响应为缓存服务器，那么 Age 表示缓存后的响应再次发起认证（向源服务器确认）到认证完成的时间值。

#### ETag

服务器使用 ETag 来唯一确定资源。当资源发生了改变后，ETag 值也会改变；另外，不同版本（如中英文版本）的 ETag 值也是不同的。

ETag 值有强弱之分，强 ETag 值表示资源对应的实体不论发生了多么细微的变化都会改变值；而对于弱 ETag 值，只有资源发生了根本改变，这个值才会改变。此时会在值之前附加 `W/`。例如

```http
ETag: W/"usagi-1234"
```

#### Location

该字段可以将响应接收方引导至另一个 URI 对应的资源（一般与重定向状态码配合使用）。

几乎所有浏览器在接收到包含该字段的响应后，都会进行**强制性**的重定向。

![image-20220919230411622](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/image-20220919230411622.png)

#### Proxy-Authenticate

用于代理服务器和客户端之间的权限认证。字段包含了**代理服务器**所要求的认证信息。

#### Retry-After

该状态码配合 503 Service Unavaliable 或者重定向响应一起使用，告知客户端在多久之后再次发送请求。常用的字段值为具体的日期时间或者创建响应后的秒数。

#### Server

包含服务器上安装的 HTTP 服务器应用程序的信息。如 `Apache/2.2.17 (Unix) PHP/5.2.5`。

#### Vary

该字段用于控制缓存，由服务器发给缓存服务器使用，传达关于本地缓存使用方法。例如：

```http
Vary: Accept-Language
```

此时，缓存服务器可以进行缓存操作，具体方式为：当缓存服务器收到获取资源的请求，并且包含首部字段 `Accept-Language`，那么就会返回缓存；否则，即时请求的为相同资源，由于和Vary指定的首部字段不同，因此必须要从源服务器重新获取资源。

#### WWW-Authenticate

用于**客户端**和**服务器**之间的 HTTP 访问认证。

关于认证相关的内容参考 [HTTP 认证](#认证机制)。

### 实体首部字段

实体首部字段是请求报文和响应报文中的实体部分所使用的首部字段。

#### Allow

当服务器接收到不支持的 HTTP 方法时，会返回状态码 405 Method Not Allowed，并且在该字段里声明其所支持的所有 HTTP 方法。

#### Content-Encoding

该字段表示服务器对实体的主体部分使用的内容编码的方式。内容编码的[具体方式](#Accept-Encoding)有这几种。

#### Content-Language

表示实体主体部分使用的自然语言。

#### Content-Length

表示实体主体部分的大小，单位为字节[^8]。

#### Content-Location

字段表示与报文主体相对应的 URI。

#### Content-MD5

该字段用于检查报文主体在传输过程中是否保持完整，并确认传输到达。

服务器对报文主体执行 MD5 算法，得到 128 位二进制数。之后再进行 Base64 编码，写入该字段。客户端会对报文再进行一次相同的 MD5 算法，两者进行比较之后就可以判断报文主体的有效性。

由于 MD5 的特性，报文如果发生了偶发性改变，是不会查到的；并且该方法无法检验处恶意篡改，因为内容和首部字段都是可以进行篡改的。

#### Content-Range

该字段针对范围请求，表示实体的具体范围。

#### Content-Type

表示实体的主体对象的媒体类型，使用 type/subtype 的形式赋值。

#### Expires

该字段告知客户端资源失效的日期。如果是缓存服务器，会以该日期为准来判断缓存的有效性。当处于有效期以内时，会直接返回缓存。如果服务器不希望缓存服务器进行缓存操作，则会将该字段置为和 Date 字段相同的值。

一般情况下，当含有 [Cache-Control](#Cache-Control) 指定的 max-age 指令时，会优先处理，并忽略 Expires 字段。

#### Last-Modified

该字段指明资源最终修改的时间。

### 与 Cookie 相关的首部字段

#### Set-Cookie（响应首部字段）

一个典型的案例：

```http
Set-Cookie: status=enable; expires=Tue, 05 Jul 2011 07:26:31 GMT; path=/; domain=.examples.com;
```

字段属性如下：

*   expires：表示浏览器可以发送 Cookie 的有效期。如果不指定，则默认为关闭浏览器即失效。
*   path：用于限制指定 Cookie 的发送范围的文件目录。但是这个限制不一定是安全的。
*   domain：该属性指定的域名与访问域名的结尾匹配一致时，都可以发送 Cookie。
*   secure：限制浏览器仅在 HTTPS 连接时，才可以发送 Cookie。
*   HttpOnly：该功能使得 JavaScript 无法获取 Cookie，可以防止跨站脚本攻击（Cross-site scripting，XSS）对 **Cookie信息的窃取**。在进行该字段设置后，Web 页面还可以对 Cookie 进行读取操作，但是使用 JavaScript 的 `document.cookie` 就无法读取 HttpOnly 属性之后的 Cookie 的内容了，防止了 XSS 中利用 JavaScript 进行 Cookie 的劫持。

#### Cookie（请求首部字段）

当客户端想要获得 HTTP 状态管理支持时，可以在请求中包含从服务器接收到的 Cookie。

### 其他首部字段

*   X-Frame-Options：该字段可以控制网站内容在其他网站的 Frame 标签内的显示问题。设置为 DENY 表示拒绝显示，而设置为 SAMEORIGIN 时，仅可以在同源域名下加载。
*   X-XSS-Protection：设置为 1 时，可以开启浏览器 XSS 防护机制。
*   DNT：表示 Do Not Track，设置为 1 时，可以拒绝网站收集个人信息。

## 确保 Web 安全的 HTTPS

### HTTP 的缺点

#### 明文传输，内容会被窃听

在通信时使用明文传输，这是 **TCP/IP** 协议族的工作机制，换句话说，通信内容在所有的通信线路上都有可能遭到窥视，即使内容是被加密的。

加密一般分为两种。一种是通信加密，一种是内容加密。

*   通信加密

HTTP 可以通过与 **SSL**（Secure Socket Layer，安全套接层）或者 TLS（Transport Layer Security，安全传输层协议）的组合使用，组成 **HTTPS**（HTTP Secure，超文本传输安全协议），来加密 HTTP 的通信内容。

*   内容加密

HTTP 协议中没有内容加密机制，因此必须要手动进行加密。但这种方式需要客户端与服务器都可以处理加密和解密信息；并且，不同于 SSL 和 TLS 对通信线路的加密机制，报文内容仍有被篡改的风险。

#### 无法验证通信双方的身份，可能遭遇伪装

在通信时，由于 HTTP 协议没有确认通信方的机制，因此任何人都可以发送请求，随之而来的，服务器也可以接收到任何人的请求，并且都会返回一个响应。

这样的机制带来的问题：

1.   无法确认请求发送至目标的 Web 服务器是不是按照真实意图返回响应的服务器，有可能是**伪装的服务器**。
2.   无法确定响应返回到的客户端是否为按照真实意图接收响应的那个客户端，有可能是**伪造的客户端**（爬虫）。
3.   无法确定对方是否具备相关资源的访问**权限**。
4.   服务器会处理所有请求，即使这个请求无意义，因此无法阻止 **DoS**（Denial of Service，拒绝服务攻击）攻击。

基于这种情况，就需要查明对方的**证书**。

证书由第三方机构颁发，很难伪造，可以证明服务器和客户端是实际存在的，对于使用者来说，也减少了个人信息泄露的危险性。

#### 无法证明报文的完整性，有遭篡改的风险

在信息传输过程中，服务器和客户端都无法得知报文是否被修改过，这种通过拦截并篡改内容的攻击称为 MITM（Man-in-the-Middle attack，中间人攻击）。

基于这种情况，需要使用校验的方法来验证报文是否正常。例如使用 MD5 或者 SHA-1 等散列值校验的方法以及数字签名方法。

### HTTPS = HTTP + 加密 + 认证 + 完整性保护

基于以上三点缺点，将 HTTP 协议加上加密、认证与数据完整性保护，即为 HTTPS。

HTTPS 并非一个新的协议，只是将 HTTP 与 SSL 和 TLS 结合起来。HTTP 直接和 TCP 进行通信，而对于 HTTPS 来说，HTTP 先和 SSL 通信，再通过 SSL 和 TCP 进行通信。这样，HTTP 就有了加密、证书和完整性保护的功能。

除了 HTTP，其他协议也可以使用 SSL 来提高安全性，如 SMTP 和 Telnet 等。

#### 公开密钥加密技术

##### 加密技术

常见的加密方法分为对称加密（Common key crypto system，共享秘钥加密）和非对称加密（Public-key cryptography，公开密钥加密）。

1.   对称加密

     使用对称加密时，加密和解密过程使用了**同一个秘钥**。但这种方式带来的问题是：在发送秘钥过程中，有被窃听的风险，如何才能安全的将秘钥转交到对方手中？如果可以将秘钥安全的发送，那么数据自然也可以安全送达，那就失去了加密的意义。

2.   非对称加密

     此时，加密和解密使用了不同的秘钥，一个为公钥（public key），另一个为私钥（private key）。

     这时候，通信过程变为：一方手中含有公钥和私钥两把钥匙，这两把钥匙是配对的，即使用公钥加密的内容，只有私钥才可以解开。一方将公钥交给另一方，在进行数据传输时，另一方使用公钥将数据加密后传送给一方，此时再使用自己的私钥，就可以解密。

需要注意的是，**公钥**和**报文**无法恢复到信息原文，<u>只能通过**私钥**</u>来进行解密，因此这是一个不可逆的过程。

##### HTTP 使用的混合加密机制

对于 HTTPS 来说，充分利用了两种方法的优势，将它们[组合](#HTTPS 的安全通信机制)起来用于通信。由于公开密钥加密与共享秘钥加密相比，虽然更安全，但是处理速度更慢，因此考虑到速度的优势，HTTPS 进行数据交换时采用共享秘钥加密方式，但是在建立通信时的秘钥传输环节，使用了公开密钥的加密方式。即：

1.   使用**更安全**的**公开密钥加密**方式，安全的**交换**在稍后共享秘钥加密中**要使用的秘钥**。
2.   确保第一步交换的秘钥是**安全**的情况下，使用**共享秘钥加密**方式进行通信。

#### 公开密钥的证书

使用了加密技术之后，虽然数据的安全性可以得到保证，但是正如[一开始](#无法验证通信双方的身份，可能遭遇伪装)所说的，无法证明某件事物是正确的、一方想要的事物，而不是中途被攻击者所替换了的事物。

解决该问题可以使用**数字证书认证机构（CA，Certificate Authority）**以及机构颁发的**公开密钥证书**。

>   服务器的运营人员会向数字证书认证机构提出**公开密钥认证**的申请，机构在判明提出申请者的身份之后，会对申请的公开密钥做数字签名，之后会分配这个已签名的公开密钥，该公开密钥会和公钥证书一起绑定。
>
>   服务器会将这份由数字认证机构颁发的公钥证书发送给客户端，使用公开密钥加密方式通信。公钥证书也可以叫做数字证书，或者直接叫证书。
>
>   拿到证书的客户端可以使用数字证书认证机构的公开密钥[^9]，对证书进行验证，一旦验证通过，客户端可以明确两件事：一、认证服务器的公开密钥的机构，是**真实有效**的数字证书认证机构。二、**服务器的公开密钥**是值得**信赖**的。

以上过程如图所示：

![image-20220922203142378](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/image-20220922203142378.png)

#### HTTPS 的安全通信机制

如图，显示了 HTTPS 的通信过程。这张图着重于应用层，而对 TCP 的连接与断开报文做出了省略。

![HTTPS](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/HTTPS.jpg)

HTTPS 通信过程的步骤：

1.   客户端通过发送 Client Hello 报文开始 SSL 通信。报文中包含客户端支持的 SSL 的指定版本、加密组件（Cipher Suite）列表（所使用的加密算法及密钥长度等）。
2.   服务器可进行 SSL 通信时，会以 Server Hello 报文作为应答。和客户端一样，在报文中包含 SSL 版本以及加密组件。服务器的加密组件内容是从接收到的**客户端加密组件**内**筛选**出来的。
3.   之后服务器发送 Certificate 报文。报文中包含公开密钥证书。
4.   最后服务器发送 Server Hello Done 报文通知客户端，最初阶段的 SSL 握手协商部分结束。
5.   SSL 第一次握手结束之后，客户端以 Client Key Exchange 报文作为回应。报文中包含通信加密中使用的一种被称为 **Pre-master secret** 的随机密码串。该报文已用**步骤 3 中的公开密钥**进行加密。
6.   接着客户端继续发送 Change Cipher Spec 报文。该报文会提示服务器，**在此报文之后的通信**会采用 **Pre-master secret** 密钥加密。
7.   客户端发送 Finished 报文。该报文包含连接至今全部报文的整体校验值。这次握手协商是否能够成功，要以服务器是否能够正确解密该报文作为判定标准。
8.   服务器同样发送 Change Cipher Spec 报文。
9.   服务器同样发送 Finished 报文。
10.   服务器和客户端的 Finished 报文交换完毕之后，SSL 连接就算建立完成。当然，通信会受到 SSL 的保护。从此处开始进行应用层协议的通信，即发送 HTTP 请求。
11.   应用层协议通信，即发送 HTTP 响应。
12.   最后由客户端断开连接。断开连接时，发送 close_notify 报文。上图做了一些省略，这步之后再发送 TCP FIN 报文来关闭与 TCP的通信。

在以上流程中，应用层发送数据时会附加一种叫做 **MAC**（Message Authentication Code）的报文摘要。MAC 能够查知报文是否遭到**篡改**，从而保护报文的**完整性**。

以上流程可以验证之前所提到的[混合加密机制](#HTTP使用的混合加密机制)。服务器在第三步发送给客户端了[公钥证书](#公开密钥的证书)，该公钥由于包含证书，因此无须担心被篡改的问题：客户端使用浏览器内嵌的数字证书认证机构的公钥，对证书进行验证，验证通过可以保证公钥的正确性。第五步，客户端会向服务端发送随机码串，该字符串即为之后真正**通信时**所使用的**共享秘钥加密方法**所使用的**秘钥**，并且，该随机码串已使用服务器第三步发送的公钥加密，并且只有服务端的私钥才可以进行破解。

3~5 步以非对称加密技术，安全的交换了秘钥之后，之后的通信可以使用对称加密技术，保证通信安全的同时，加快了处理报文的速度。

整个流程的图解如下：

![HTTPS1](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/HTTPS1.jpg)

#### HTTPS 的弊端

HTTPS 使用 SSL 时，速度不可避免的会变慢。

一方面是通信慢：使用 HTTPS 时，除了 TCP 连接、HTTP 请求 · 响应以外，还有 SSL 通信。

另一方面，针对加密和解密过程，会消耗更多的硬件资源。

针对该问题可以使用 **SSL加速器** 这一硬件，专门为 SSL 通信进行服务，可以提高 SSL 的计算速度，分担负载。

## 认证机制

较常用的认证信息有：

*   密码：仅本人所知的字符串信息
*   动态令牌：本人持有的设备里的**一次性**的、**动态刷新**的密码
*   数字证书：本人/终端持有的信息
*   生物认证：指纹、虹膜等本人的生理信息
*   IC 卡等

HTTP 常用的认证方式有：

*   BASIC 认证（基本认证）
*   DIGEST 认证（摘要认证）
*   SSL 客户端认证
*   FromBase 认证（基于表单的认证）

### BASIC 认证

![BASIC](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/BASIC.jpg)

BASIC认证的步骤如下：

1.   服务器在需要进行认证时，返回 401 Authorization Required，并且带有首部字段 [WWW-Authenticate](#WWW-Authenticate)，字段内包含认证的方式，即 BASIC，以及 Request-URI 安全与字符串。
2.   客户端将用户 ID 与密码发送给服务器（写入首部 [Authorization](#Authorization) 中），中间通过冒号（:）连接，再经过 Base64 编码处理。
3.   服务器在进行验证并通过之后，会返回一条包含 Request-URI 资源的响应。

注意：BASIC 采用的 Base64 编码不是加密处理，因此密码容易被窃听（HTTPS 通信时不会）。

### DIGEST 认证

![DIGEST](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/DIGEST.jpg)

该方法避免了信息泄露的风险。

1.   服务器在需要客户端进行认证时，返回 401 Authorization Required 以及首部字段 [WWW-Authenticate](#WWW-Authenticate)，首部字段内包含 realm 和 nonce 两个字段，其中，nonce 表示一个随机字符串，一般为 Base64 编码。
2.   客户端返回的 [Authorization](#Authorization) 中包含 username、realm、nonce、uri 和 response 的字段信息。realm 和 nonce 是之前服务器响应的内容，而 username 是 realm 限定范围内可以进行认证的用户名，uri 即 Request-URI 的值，response 即为经过 MD5 运算后的密码字符，也就是 Request-Digest。
3.   服务端接收到认证信息后，进行确认，确认后返回包含 Request-URI 资源的响应，并在 Authentication-Info 首部字段中加入一些认证成功的信息。

>   DIGEST 认证提供防止密码被窃听的保护机制，但并不存在防止用户伪装的保护机制。

### SSL 客户端认证

以上提到的两种认证都在使用上不那么便捷灵活，也达不到多数 Web 网站对高度安全等级的追求标准，因此适用范围有所受限。

SSL 认证可以避免用户密码被盗的情况，因此该认证方式使用客户端证书，服务器收到客户端证书之后，验证通过即可领取客户端内的公开密钥，之后就可以开始 HTTPS 加密通信了。

### 基于表单的认证

但是一般情况下，SSL 客户端认证不会仅依靠证书，一般会使用和基于表单认证相结合的双因素认证方式。再认证过程中，不仅仅需要密码，还需要申请认证者提供其他持有信息。也就是说，SSL 客户端证书用来认证**客户端计算机**，另一个认证因素密码用来确认这是用户本人。

HTTP 协议中并没有定义基于表单的认证方法，而是通过服务器上的 Web 应用程序发送登录信息，通过应用程序来进行认证。

BASIC 认证和 DIGEST 认证由于安全性问题，使用受限，而 SSL 客户端证书认证需要面对繁琐的导入以及维护费用等问题，因此这种基于表单的认证方式也是最为常见的方式。

### Session 及 Cookie 管理

由于 HTTP 无状态协议的特点，如果认证成功，之后的操作不应该再次进行认证，因此必须有一个机制来保存用户的状态。否则用户下一次操作，服务器会认为他和其他用户一样没有通过认证。

状态管理可以用 Session 和 Cookie 机制，如图：

![](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/session.jpg)

简而言之，客户端进行认证，成功之后，服务器会使用 Session 机制来记录用户状态。服务器给客户端发放识别用户的 SessionID，这个 ID 会和用户状态进行绑定。之后服务器的响应里会包含 [Set-Cookie](#Set-Cookie（响应首部字段）) 首部，写入 SessionID，例如（JSESSIONID = XXXX），客户端会将这个 SessionID 作为 Cookie 保存。

对于 SessionID 来说，需要防止其被盗用，因此 SessionID 需要使用加密后的字符串，同时，客户端应该对 SessionID 有**过期时间**的限制。

为了防止跨站脚本攻击，还需要指定 Cookie 首部字段属性 HttpOnly。

## 基于 HTTP 的功能追加协议

### SPDY 协议——解决性能瓶颈

#### HTTP 的瓶颈

大量内容同时发生频繁改变，如何更新？

*   一条连接只可以发送一个请求
*   请求只可以从**客户端**发，客户端只能接收响应
*   首部信息多，冗余，造成资源浪费
*   首部未压缩

#### Ajax 解决方法

Ajax（Asynchronous JavaScript and XML，异步 JavaScript 与 XML 技术），可以利用 JavaScript 和 DOM（Document Object Model，文档对象模型）的操作，达到**局部**Web页面**替换加载**的**异步**通信手段。（只更新一部分页面，因此数据量减小）

![ajax](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/ajax.jpg)

#### Comet 解决方法

该方法即为长连接，模拟服务端进行推送。即：服务端接收到请求之后，会挂起响应，之后当需要有内容更新时，再返回响应。这种方法可以做到实时更新，但是为了保持连接，会消耗很多资源。

![comet](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/comet.jpg)

#### SPDY

SPDY 在 TCP/IP 的应用层与传输层之间加入了会话层，主要负责控制对**数据的流动**，并且不影响处于应用层的 HTTP 协议。

这样一来，HTTP 协议获得以下功能：

1.   多路复用：一个 TCP 连接可以处理多个 HTTP 请求。
2.   请求被赋予优先级：可以解决因为多个请求，低带宽导致的响应变慢的问题。
3.   压缩 HTTP 首部
4.   推送功能
5.   服务器提示客户端请求所需的资源

### WebSocket 协议——全双工通信

当双方建立起 WebSocket 协议的通信之后，客户端与服务器都可以互相发送数据，即为**全双工**。该通信协议是长连接的。

该通信的建立需要 HTTP 连接已经建立，由客户端发送改变协议的请求。

客户端使用 [Upgrade](#Upgrade) 首部字段发送请求：

```http
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Origin: http://example.com
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
```

Sec-WebSocket-Key 字段内记录着握手过程中必不可少的键值。Sec-WebSocket-Protocol 字段内记录使用的子协议。

服务器收到改变协议的请求之后，返回状态码 101 Switching Protocols 的响应。

```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
Sec-WebSocket-Protocol: chat
```

Sec-WebSocket-Accept 的字段值是由握手请求中的 Sec-WebSocket-Key 的字段值生成的。

成功握手确立 WebSocket 连接之后，通信时不再使用 HTTP 的数据帧，而采用 WebSocket 独立的数据帧。如图：

![ws](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/ws.jpg)

### HTTP/2.0

HTTP/2的通过支持请求与响应的多路复用来减少延迟，通过压缩HTTP首部字段将协议开销降至最低，同时增加对请求优先级和服务器端推送的支持。HTTP/2.0 具体的变化有[^10]：

#### 二进制分帧

>   **帧：**HTTP/2 数据通信的最小单位消息：指 HTTP/2 中逻辑上的 HTTP 消息。例如请求和响应等，消息由一个或多个帧组成。
>
>   **流：**存在于连接中的一个虚拟通道。流可以承载双向消息，每个流都有一个唯一的整数ID。
>
>   HTTP/2 采用二进制格式传输数据，而非 HTTP 1.x 的文本格式，二进制协议解析起来更高效。 HTTP/1 的请求和响应报文，都是由起始行，首部和实体正文（可选）组成，各部分之间以文本换行符分隔。HTTP/2 将请求和响应数据分割为更小的帧，并且它们采用二进制编码。
>
>   **HTTP/2 中，同域名下所有通信都在单个连接上完成，该连接可以承载任意数量的双向数据流。**每个数据流都以消息的形式发送，而消息又由一个或多个帧组成。多个帧之间可以乱序发送，根据帧首部的流标识可以重新组装。

#### 多路复用

>   多路复用，代替原来的序列和阻塞机制。所有就是请求的都是通过一个 TCP 连接并发完成。 HTTP 1.x 中，如果想并发多个请求，必须使用多个 TCP 链接，且浏览器为了控制资源，还会对单个域名有 6-8个的TCP链接请求限制。
>
>   在 HTTP/2 中，有了二进制分帧之后，HTTP/2 不再依赖 TCP 链接去实现多流并行了，在 HTTP/2中：
>
>   +   同域名下所有通信都在单个连接上完成。
>   +   单个连接可以承载任意数量的双向数据流。
>   +   数据流以消息的形式发送，而消息又由一个或多个帧组成，多个帧之间可以**乱序**发送，因为根据帧首部的**流标识**可以**重新组装**。
>
>   这一特性，使性能有了极大提升：
>
>   +   **同个域名只需要占用一个 TCP 连接**，消除了因多个 TCP 连接而带来的延时和内存消耗。
>   +   单个连接上可以并行交错的请求和响应，之间互不干扰。
>   +   在 HTTP/2 中，每个请求都可以带一个 31bit 的优先值，0 表示最高优先级， 数值越大优先级越低。有了这个优先值，客户端和服务器就可以在处理不同的流时采取不同的策略，以最优的方式发送流、消息和帧。

#### 服务器推送

>   服务端可以在发送页面 HTML 时主动推送其它资源，而不用等到浏览器解析到相应位置，发起请求再响应。例如服务端可以主动把 JS 和 CSS 文件推送给客户端，而不需要客户端解析 HTML 时再发送这些请求。
>
>   服务端可以主动推送，客户端也有权利选择是否接收。如果服务端推送的资源已经被浏览器缓存过，浏览器可以通过发送 RST_STREAM 帧来拒收。主动推送也遵守同源策略，服务器不会随便推送第三方资源给客户端。

#### 头部压缩

>   +   HTTP/2 在客户端和服务器端使用“首部表”来跟踪和存储之前发送的键－值对，对于相同的数据，不再通过每次请求和响应发送；
>   +   首部表在 HTTP/2 的连接存续期内始终存在，由客户端和服务器共同渐进地更新;
>   +   每个新的首部键－值对要么被追加到当前表的末尾，要么替换表中之前的值。

### WebDAV 协议——管理服务器文件

WebDAV（Web-based Distributed Authoring and Versioning，基于万维网的分布式创作和版本控制）协议可以对 Web 服务器上的内容资源（一般是文件）进行创建、复制、编辑、删除、对文件加锁等操作的分布式文件系统。

WebDAV定义的属性：

*   集合（Collection）：是一种统一管理多个资源的概念。以集合为单位可进行各种操作。也可实现类似集合的集合这样的叠加。
*   资源（Resource）：把文件或集合称为资源。
*   属性（Property）：定义资源的属性。定义以“名称 = 值”的格式执行。
*   锁（Lock）：把文件设置成无法编辑状态。多人同时编辑时，可防止在同一时间进行内容写入。

该协议也对 HTTP/1.1 中追加了一些方法与状态码：

*   PROPFIND ：获取属性

*   PROPPATCH ：修改属性

*   MKCOL ：创建集合

*   COPY ：复制资源及属性

*   MOVE ：移动资源

*   LOCK ：资源加锁

*   UNLOCK ：资源解锁

    状态码扩展：

*   102 Processing ：可正常处理请求，但目前是处理中状态
*   207 Multi-Status ：存在多种状态
*   422 Unprocessible Entity ：格式正确，内容有误
*   423 Locked ：资源已被加锁
*   424 Failed Dependency ：处理与某请求关联的请求失败，因此不再维持依赖关系
*   507 Insufficient Storage ：保存空间不足

## 构建 Web 内容的技术

### HTML

>   HTML（HyperText Markup Language，超文本标记语言）。超文本是一种文档系统，可将文档中任意位置的信息与其他信息（文本或图片等）建立关联，即超链接文本。标记语言是指通过在文档的某部分穿插特别的字符串标签，用来修饰文档的语言。我们把出现在 HTML 文档内的这种特殊字符串叫做 HTML 标签（Tag）。

```html
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>hackr.jp</title>
<style type="text/css">
.logo {
padding: 20px;
text-align: center;
}<
/style>
</head>
<body>
<div class="logo">
	<p><img src="photo.jpg" alt="photo" width="240" height="127" /></p>
	<p><img src="hackr.gif" alt="hackr.jp" width="240" height="84" /></p>
	<p><a href="http://hackr.jp/">hackr.jp</a> </p>
</div>
</body>
</html>
```

### CSS

>   CSS（Cascading Style Sheets，层叠样式表）可以指定如何展现 HTML内的各种元素，属于样式表标准之一。即使是相同的 HTML 文档，通过改变应用的 CSS，用浏览器看到的页面外观也会随之改变。CSS的理念就是让文档的**结构**和**设计**分离，达到**解耦**的目的。

```css
.logo {
	padding: 20px;
	text-align: center;
}
```

### 动态 HTML

>   动态 HTML 可以使用客户端脚本语言 JavaScript，实现对 HTML 的 Web 页面的动态改造。DOM 是用以操作 HTML 文档和 XML 文档的 API（Application Programming Interface，应用编程接口）。使用 DOM 可以将 HTML 内的**元素当作对象操作**，如**取出**元素内的**字符串**、**改变**那个 CSS 的**属性**等，使页面的设计发生**改变**。

例如，对如下 HTML 文档（\<p\> 标签表示段落）：

```html
<body>
	<h1>繁琐的Web安全</h1>
	<p>第Ⅰ部分 Web的构成元素</p>
	<p>第Ⅱ部分 浏览器的安全功能</p>
	<p>第Ⅲ部分 接下来发生的事</p>
</body>
```

如果想对其第三个段落（索引为 2）改变文字颜色，则可以嵌入如下代码：

```javascript
<script type="text/javascript">
	var content = document.getElementsByTagName('P');
	content[2].style.color = '#FF0000';
</script>
```

### Web 应用

Web 服务器不仅仅可以返回实现已经写好的静态内容（网页等资源），也可以通过**运行**服务器上的**程序**，返回动态内容，该内容是可变的。

#### CGI

>   CGI（Common Gateway Interface，通用网关接口）是指 Web 服务器在接收到客户端发送过来的请求后转发给程序的一组机制。在 CGI 的作用下，程序会对请求内容做出相应的动作，比如创建 HTML 等动态内容。CGI 程序通常是用 Perl、PHP、Ruby 和 C 等编程语言编写而成。

即：客户端发送请求至服务器，服务器通过运行 CGI 程序，获得所需要的响应。

#### Servlet

Servlet（Servlet = Server + Applet）是用 Java 语言实现的程序。[CGI](#CGI) 每次接到请求，程序都要重新启动，执行一次，因此需要考虑负载问题。而 Servlet 运行在一个进程中，运行环境成为 Web 容器或者 Servlet 容器。

### 数据格式以及语言

#### 可扩展标记语言 XML

>   XML（eXtensible Markup Language，可扩展标记语言）是一种可按应用目标进行扩展的通用标记语言。旨在通过使用 XML，使互联网数据共享变得更容易。

>   XML 和 HTML 都是从标准通用标记语言 SGML（Standard Generalized Markup Language）简化而成。与 HTML 相比，它对数据的记录方式做了特殊处理。和 HTML 一样，使用标签构成**树形结构**，并且可**自定义扩展标签**。从 XML 文档中读取数据比起 HTML **更为简单**。由于 XML 的结构基本上都是用**标签分割而成的树形结构**，因此通过语法分析器（Parser）的解析功能解析 XML 结构并取出数据元素，可更容易地对数据进行读取。

由于 XML 更容易读取的特点，使得 XML 可以用在 2 个不同应用之间的**数据交换格式化**[^11]。

#### RSS/Atom

>   RSS（简易信息聚合，也叫聚合内容）和 Atom 都是发布新闻或博客日志等更新信息文档的格式的总称。两者都用到了 XML。

#### JSON

>   JSON（JavaScript Object Notation）是一种以 JavaScript（ECMAScript）的对象表示法为基础的轻量级数据标记语言。能够处理的数据类型有 false/null/true/ 对象 / 数组 / 数字 / 字符串，这 7 种类型。

```json
{"name": "Web Application Security", "num": "TR001"}
```

JSON 让数据更轻更纯粹，并且 JSON 的字符串形式可被 JavaScript 轻易地读入[^11]。

## Web 的攻击技术

### 针对 Web 的攻击技术

简单的 HTTP 协议本身没有安全性问题，因此容易受到攻击的主要是应用 HTTP 协议的服务器与客户端，以及运行在服务器上的 Web 应用。

HTTP 请求的所有内容，在客户端可以进行自由的变更与篡改，因此请求报文中加载攻击代码，就可以发起攻击，如在 URL、HTTP 首部、Cookie 中都可以插入攻击代码。

针对 Web 应用的攻击模式主要分两种：主动攻击和被动攻击。

主动攻击是攻击者**直接**访问 Web 应用，针对服务器上的资源进行攻击，如 **SQL 注入攻击**和 **OS 命令**注入攻击。

被动攻击是利用圈套策略执行攻击代码的模式，**不会直接**对目标 Web 应用访问发起攻击。一般来说，会通过诱导用户触发（利用用户的浏览器或者邮件客户端）已经设置好的陷阱，陷阱会启动发送已嵌入攻击代码的 HTTP 请求。执行完攻击代码之后，可能会使用户所持有的 Cookie 等信息被窃取，用户权限遭到滥用等后果。典型的如跨站脚本攻击和跨站点请求伪造。

被动攻击还可以发起对**企业内部网络**的攻击。

### 因输出值转义不完全引发的安全漏洞

一般情况下，采用 JavaScript 进行客户端数据验证，但是客户端是可以篡改数据或者关闭 JavaScript 的，因此还需要在服务器进行数据规范验证。JavaScript 方法只是为了尽早辨别输入错误，优化 UI 交互体验。

#### 跨站脚本攻击

例如，对于如下 URL：

```http
http://example.com/login?id=xxxx
```

包含攻击代码的 URL 为：

```http
http://example.com/login?id=""
```



### 因设置或设计上的缺陷引发的安全漏洞

### 因回话管理疏忽引发的安全漏洞

### 其他安全漏洞

[^1]: 对无状态的说明：HTTP 协议自身不会对请求与响应做持久化处理，主要是为了更快处理**大量事务**
[^2]: 在 REST 架构的网站中，PUT 和 POST 都可以向 HTTP 服务器提交数据，但 PUT 一般是幂等的。若不存在则新建内容，若存在则修改内容，具体见 [RFC文档](https://www.rfc-editor.org/rfc/rfc7231)。
[^3]: 对于多重范围的请求，响应的首部字段会设置 `Content-Type: multipart/byteranges` 后返回响应报文。
[^4]: 303 与 302 类似，但是 303 状态码明确表示客户端应当用 **GET方法** 获取资源。
[^5]: 301、302、303 响应状态码返回时，几乎所有浏览器都会把 POST 改成 GET，之后再次发送请求，但实际上，301 和 302 是禁止这么做的。
[^6]: 比如首部信息包含：`If-Match`、`If-Modified-Since`、`If-None-Match`、`If-Range`、`If-Unmodified-Since`
[^7]: 注意区别：no-cache 必须做强制性的有效性校验，而 no-store 侧重于真正的不存储任何资源。

[^8]: 当对实体的主体使用了内容编码时，不能再使用该字段。

[^9]: 认证机构的公开密钥一般是在浏览器内部植入的。
[^10]: https://zhuanlan.zhihu.com/p/26559480
[^11]:XML 格式的文件常见与 SOAP 架构的软件，而 JSON 多用于 RESTful 架构的软件。具体可参考https://www.redhat.com/zh/topics/integration/whats-the-difference-between-soap-rest
