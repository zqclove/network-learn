# HTTP

# 基础概念

## URI

URI 包含 URL 和 URN。

![URI](C:\Users\Administrator\Desktop\学习\网络\network-learn\img\URI.png)

## 请求和响应报文

### 1. 请求报文

客户端发送一个请求报文给服务器，服务器根据请求报文中的信息进行处理，并将处理结果放入响应报文中返回给客户端。

请求报文结构：

- 第一行是包含了请求方法、URL、协议版本；
- 接下来的多行都是请求首p部 Header，每个首部都有一个首部名称，以及对应的值。
- 一个空行用来分隔首部和内容主体 Body
- 最后是请求的内容主体

![请求报文](C:\Users\Administrator\Desktop\学习\网络\network-learn\img\请求报文.png)

### 2. 响应报文

响应报文结构：

- 第一行包含协议版本、状态码以及描述，最常见的是 200 OK 表示请求成功了
- 接下来多行也是首部内容
- 一个空行分隔首部和内容主体
- 最后是响应的内容主体

![响应报文](C:\Users\Administrator\Desktop\学习\网络\network-learn\img\响应报文.png)

# HTTP方法

客户端发送的   **请求报文**   第一行为请求行，包含有方法字段。方法有以下几种：

## GET

> 获取资源

当前网络请求中，绝大部分使用过的是GET方法。通常用于请求服务器发送某个资源。

## HEAD

> 获取报文首部

和 GET 方法类似，但是不返回报文实体主体部分。

主要用于确认 URL 的有效性以及资源更新的日期时间等。

这就允许客户端在未获取实际资源的情况下，对资源的首部进行检查。
    使用HEAD方法有以下优点：

- 在不获取资源的情况下了解资源的情况（比如：判断其类型）；
- 通过查看响应的状态码，看看某个对象是否存在；
- 通过查看首部，测试资源是否被修改了

​    必须确保返回的首部与GET请求所返回的首部完全相同。

## POST

> 传输实体主体

POST 主要用来传输数据，而 GET 主要用来获取资源。

POST方法期初是用来向服务器输入数据的，实际上，通常会用它来支持HTML的表单。表单中填好的数据通常会被送给服务器，然后由服务器将其发送到它要去的地方。

*GET与 POST的比较在下面叙述*

## PUT

> 上传文件

由于自身不带验证机制，任何人都可以上传文件，因此存在安全性问题，一般不使用该方法。

```html
PUT /new.html HTTP/1.1
Host: example.com
Content-type: text/html
Content-length: 16

<p>New File</p>
```

PUT方法会向服务器写入文件。PUT方法的语义就是让服务器用请求的主体部分来创建一个由所请求的URL命名的新文件，或者替换已存在的URL。

因为PUT允许用户对内容进行修改，所以很多Web服务器都要求在执行PUT之前，用密码登录。

## PATCH

> 对资源进行部分修改

PUT 也可以用于修改资源，但是只能完全替代原始资源，PATCH 允许部分修改。

```html
PATCH /file.txt HTTP/1.1
Host: www.example.com
Content-Type: application/example
If-Match: "e0023aa4e"
Content-Length: 100

[description of changes]
```



## DELETE

> 删除文件

与 PUT 功能相反，并且同样不带验证机制。

```html
DELETE /file.html HTTP/1.1
```



## OPTIONS

> 查询支持的方法

查询指定的 URL 能够支持的方法。

会返回 `Allow: GET, POST, HEAD, OPTIONS` 这样的内容。

## CONNECT

> 要求在与代理服务器通信时建立隧道

使用 SSL（Secure Sockets Layer，安全套接层）和 TLS（Transport Layer Security，传输层安全）协议把通信内容加密后经网络隧道传输。

```
CONNECT www.example.com:443 HTTP/1.1
```

![http-connect](C:\Users\Administrator\Desktop\学习\网络\network-learn\img\http-connect.jpg)

## TRACE

> 追踪路径

服务器会将通信路径返回给客户端。

发送请求时，在 Max-Forwards 首部字段中填入数值，每经过一个服务器就会减 1，当数值为 0 时就停止传输。

通常不会使用 TRACE，并且它容易受到 XST 攻击（Cross-Site Tracing，跨站追踪）。

## POST与GET的比较

### 作用

GET 用于获取资源，而 POST 用于传输实体主体。

### 参数

GET 和 POST 的请求都能使用额外的参数，但是 GET 的参数是以查询字符串出现在 URL 中，而 POST 的参数存储在实体主体中。不能因为 POST 参数存储在实体主体中就认为它的安全性更高，因为照样可以通过一些抓包工具（Fiddler）查看。

因为 URL 只支持 ASCII 码，因此 GET 的参数中如果存在中文等字符就需要先进行编码。例如 `中文` 会转换为 `%E4%B8%AD%E6%96%87`，而空格会转换为 `%20`。POST 参数支持标准字符集。

```html
GET /test/demo_form.asp?name1=value1&name2=value2 HTTP/1.1
POST /test/demo_form.asp HTTP/1.1
Host: w3schools.com
name1=value1&name2=value2
```

### 安全

**安全的 HTTP 方法不会改变服务器状态，也就是说它只是可读的。**

GET 方法是安全的，而 POST 却不是，因为 POST 的目的是传送实体主体内容，这个内容可能是用户上传的表单数据，上传成功之后，服务器可能把这个数据存储到数据库中，因此状态也就发生了改变。

安全的方法除了 GET 之外还有：HEAD、OPTIONS。

不安全的方法除了 POST 之外还有 PUT、DELETE。

***这里的安全是指 GET 方法不会修改服务器中的资源，而 POST 会***

**PS：一般在实际开发中，涉及安全问题都是使用https协议。**

### 幂等性

幂等的 HTTP 方法，同样的请求被执行一次与连续执行多次的效果是一样的，服务器的状态也是一样的。换句话说就是，幂等方法不应该具有副作用（统计用途除外）。

所有的安全方法也都是幂等的。

在正确实现的条件下，GET，HEAD，PUT 和 DELETE 等方法都是幂等的，而 POST 方法不是。

GET /pageX HTTP/1.1 是幂等的，连续调用多次，客户端接收到的结果都是一样的：

```html
GET /pageX HTTP/1.1
GET /pageX HTTP/1.1
GET /pageX HTTP/1.1
GET /pageX HTTP/1.1
```

POST /add_row HTTP/1.1 不是幂等的，如果调用多次，就会增加多行记录：

```html
POST /add_row HTTP/1.1   -> Adds a 1nd row
POST /add_row HTTP/1.1   -> Adds a 2nd row
POST /add_row HTTP/1.1   -> Adds a 3rd row
```

DELETE /idX/delete HTTP/1.1 是幂等的，即使不同的请求接收到的状态码不一样：

```html
DELETE /idX/delete HTTP/1.1   -> Returns 200 if idX exists
DELETE /idX/delete HTTP/1.1   -> Returns 404 as it just got deleted
DELETE /idX/delete HTTP/1.1   -> Returns 404
```

### 可缓存

如果要对响应进行缓存，需要满足以下条件：

- 请求报文的 HTTP 方法本身是可缓存的，包括 GET 和 HEAD，但是 PUT 和 DELETE 不可缓存，POST 在多数情况下不可缓存的。
- 响应报文的状态码是可缓存的，包括：200, 203, 204, 206, 300, 301, 404, 405, 410, 414, and 501。
- 响应报文的 Cache-Control 首部字段没有指定不进行缓存。

### XMLHttpRequest

为了阐述 POST 和 GET 的另一个区别，需要先了解 XMLHttpRequest：

> XMLHttpRequest 是一个 API，它为客户端提供了在客户端和服务器之间传输数据的功能。它提供了一个通过 URL  来获取数据的简单方式，并且不会使整个页面刷新。这使得网页只更新一部分页面而不会打扰到用户。XMLHttpRequest 在 AJAX  中被大量使用。

- 在使用 XMLHttpRequest 的 POST 方法时，浏览器会先发送 Header 再发送 Data。但并不是所有浏览器会这么做，例如火狐就不会。
- 而 GET 方法 Header 和 Data 会一起发送。



# HTTP状态码

​	 每一个请求报文发出后，都能收到一个响应报文。**响应报文**的第一行就是状态行，包含了三项内容，即HTTP的版本号、状态码以及解释状态码的简单语句，用于告知客户端请求的结果。

| 状态码 | 类别                             | 含义                                             |
| ------ | -------------------------------- | ------------------------------------------------ |
| 1XX    | Informational（信息性状态码）    | 接收的请求正在处理                               |
| 2XX    | Success（成功状态码）            | 请求正常处理完毕                                 |
| 3XX    | Redirection（重定向状态码）      | 需要进行附加操作以完成请求                       |
| 4XX    | Client Error（客户端错误状态码） | 服务器无法处理请求，请求中有错误的语法或不能完成 |
| 5XX    | Server Error（服务器错误状态码） | 服务器处理请求出错                               |

## 1XX 信息性状态码

- **100 Continue**：服务器仅接收到部分请求，但是一旦服务器并没有拒绝该请求，客户端应该继续发送其余的请求。
- **101 Switching Protocols**：服务器转换协议：服务器将遵从客户的请求转换到另外一种协议。

## 2XX 成功状态码

- **200 OK**：请求成功（其后是对GET和POST请求的应答文档）。
- **201 Created**：请求被创建完成，同时新的资源被创建。
- **202 Accepted**：供处理的请求已被接受，但处理未完成。
- **203 Non-authoritative Information**：文档已经正常地返回，但一些应答头可能不正确，因为使用的是文档的拷贝。
- **204 No Content**：请求处理成功，但返回的响应报文不包含实体的主体部分。浏览器应该继续显示原来的文档。如果用户定期地刷新页面，而Servlet可以确定用户文档足够新，这个状态代码是很有用的。
- **205 Reset Content**：请求处理成功，但返回的响应报文不包含实体的主体部分。但浏览器应该重置它所显示的内容。用来强制浏览器清除表单输入内容（刷新表单）。
- **206 Partial Content**：表示客户端进行了范围请求，响应报文包含由 Content-Range 指定范围的实体内容。

## 3XX 重定向状态码

- **301 Moved Permanently**  ：永久性重定向

- **302 Found**  ：临时性重定向

- **303 See Other**  ：和 302 有着相同的功能，但是 303 明确要求客户端应该采用 GET 方法获取资源。

    注：虽然 HTTP 协议规定 301、302 状态下重定向时不允许把 POST 方法改成 GET 方法，但是大多数浏览器都会在 301、302 和 303 状态下的重定向把 POST 方法改成 GET 方法。

- **304 Not Modified**  ：如果请求报文首部包含一些条件，例如：If-Match，If-Modified-Since，If-None-Match，If-Range，If-Unmodified-Since，如果不满足条件，则服务器会返回 304 状态码。

- **305 Use Proxy**：客户请求的文档应该通过Location头所指明的代理服务器提取。

- **306 *Unused***：此代码（状态码）被用于前一版本。目前已不再使用，但是代码依然被保留。

- **307 Temporary Redirect**  ：临时重定向，与 302 的含义类似，但是 307 要求浏览器不会把重定向请求的 POST 方法改成 GET 方法。

## 4XX 客户端错误状态码

HTTP中关于4XX的状态码有很多，这里只列出部分常见且重要的。

- **400 Bad Request**  ：请求报文中存在语法错误。
- **401 Unauthorized**  ：该状态码表示发送的请求需要有认证信息（BASIC 认证、DIGEST 认证）。如果之前已进行过一次请求，则表示用户认证失败。
- **403 Forbidden**  ：请求被拒绝。
- **404 Not Found**：服务器无法找到请求的这个页面。

## 5XX 服务器错误状态码

只列出部分状态码。

- **500 Internal Server Error**  ：服务器正在执行请求时发生不可预知的错误。
- **503 Service Unavailable**  ：服务器暂时处于超负载或正在进行停机维护，现在无法处理请求。

# HTTPS

​	HTTPS （全称：Hyper Text Transfer Protocol over SecureSocket Layer），是以安全为目标的 HTTP 通道，在HTTP的基础上通过**传输加密**和**身份认证**保证了传输过程的安全性 [1] 。**HTTPS 在HTTP 的基础下加入SSL 层**，HTTPS 的安全基础是 SSL，因此加密的详细内容就需要 SSL。 **HTTPS 存在不同于 HTTP 的默认端口及一个加密/身份验证层（在 HTTP与TCP 之间）。**这个系统提供了身份验证与加密通讯方法。它被广泛用于万维网上安全敏感的通讯，例如交易支付等方面 。

## https加密方式



## http与https的区别

1. http是明文传输；https会使用加密方式传输信息（非对称密钥和对称密钥混合的方式）；
2. http端口号是80，https端口号是443；
3. https需要申请证书（数字证书）；

# 参考资料

[HTTP协议——HTTP方法]: https://blog.csdn.net/vikeyyyy/article/details/80655115
[HTTP]: https://github.com/CyC2018/CS-Notes/blob/master/notes/HTTP.md#post

