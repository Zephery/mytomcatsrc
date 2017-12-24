# TCP/IP、HTTP、HTTPS、HTTP2.0
HTTP，全称超文本传输协议（HTTP，HyperText Transfer Protocol)，是一个客户端和服务器端请求和应答的标准（TCP），互联网上应用最为广泛的一种网络协议。客户端是终端用户，服务器端是网站。通过使用Web浏览器、网络爬虫或者其它的工具，客户端发起一个到服务器上指定端口（默认端口为80）的HTTP请求。  

HTTPS，即加密后的HTTP。HTTP协议传输的数据都是未加密的，也就是明文的，因此使用HTTP协议传输隐私信息非常不安全。HTTPS都是用的TLS协议，但是由于SSL出现的时间比较早，并且依旧被现在浏览器所支持，因此SSL依然是HTTPS的代名词，但无论是TLS还是SSL都是上个世纪的事情，SSL最后一个版本是3.0，今后TLS将会继承SSL优良血统继续为我们进行加密服务。目前TLS的版本是1.2，定义在RFC 5246中，暂时还没有被广泛的使用。  

HTTP2.0，下一代的HTTP协议。相比于HTTP1.x，大幅度的提升了web性能，进一步减少了网络延时和拥塞。  
<div align="center">

![](http://image.wenzhihuai.com/images/20171224034237.png)

</div>


各自的RFC相关文档自己去搜吧，[https://www.rfc-editor.org/](https://www.rfc-editor.org/)。

# 一、TCP/IP
为了了解HTTP，有必要先理解一下TCP/IP。目前，存在两种划分模型的方法，OSI七层模型和TCP/IP模型，具体的区别不在阐述。HTTP是建立在TCP协议之上，所以HTTP协议的瓶颈及其优化技巧都是基于TCP协议本身的特性，例如tcp建立连接的3次握手和断开连接的4次挥手以及每次建立连接带来的RTT延迟时间。
<div align="center">

![](http://image.wenzhihuai.com/images/20171221101751.png)

</div>




# 二、HTTP
超文本传输协议(HyperText Transfer Protocol) 是伴随着计算机网络和浏览器而诞生，[在浏览器出现之前，人们是怎么使用网络的？](http://www.ifanr.com/550613)，不管怎么说，那个时代对于现在的我们，有点难以想象。。。之后，网景发布了Netscape Navigator浏览器，才慢慢打开了互联网的幕布。如果根据OSI来划分的话，HTML属于表示层，而HTTP属于应用层。HTTP发展至今，经过了HTTP0.9、HTTP1.0、HTTP1.1、HTTP2.0的时代，虽然2.0很久之前就正式提出标准，大多浏览器也支持了，但是网络支持HTTP2.0的却很少。
## 2.1 HTTP报文分析
报文，是网络中交换和传输的基本单元，即一次性发送的数据块。HTTP的报文是由一行一行组成的，纯文本，而且是明文，即：如果能监听你的网络，那么你发送的所有账号密码都是可以看见的，为了保障数据隐秘性，HTTPS随之而生。
### 2.1.1 请求报文：
为了形象点，我们把报文标准和实际的结合起来看：
<div align="center">

![](http://image.wenzhihuai.com/images/20171221111615.png)

</div>

下面是实际报文，以访问自己的网站([http://www.wenzhihuai.com](http://www.wenzhihuai.com))中的一个链接为例:
<div align="center">

![](http://image.wenzhihuai.com/images/20171221043103.png)

</div>

#### 请求行
请求行由方法字段、URL 字段 和HTTP 协议版本字段 3 个部分组成，他们之间使用空格隔开。常用的 HTTP 请求方法有 GET、POST、HEAD、PUT、DELETE、OPTIONS、TRACE、CONNECT，这里我们使用的是GET方法，访问的是/biaoqianyun.do，协议使用的是HTTP/1.1。  
● GET：当客户端要从服务器中读取某个资源时，使用GET 方法。如果需要加传参数的话，需要在URL之后加个"?"，然后把参数名字和值用=连接起来，传递参数长度受限制，通常IE8的为4076，Chrome的为7675。例如，/index.jsp?id=100&op=bind。  
● POST：当客户端给服务器提供信息较多时可以使用POST 方法，POST 方法向服务器提交数据，比如完成表单数据的提交，将数据提交给服务器处理。GET 一般用于获取/查询资源信息，POST 会附带用户数据，一般用于更新资源信息。POST 方法将请求参数封装在HTTP 请求数据中，以名称/值的形式出现，可以传输大量数据;  
#### 请求头部
请求头部由关键字/值对组成，每行一对，关键字和值用英文冒号“:”分隔。请求头部通知服务器有关于客户端请求的信息，典型的请求头有：  
**User-Agent**：产生请求的浏览器类型; 
**Accept**：客户端可识别的响应内容类型列表;星号 “ * ” 用于按范围将类型分组，用 “ */* ” 指示可接受全部类型，用“ type/* ”指示可接受 type 类型的所有子类型;
**Accept-Language**：客户端可接受的自然语言;  
**Accept-Encoding**：客户端可接受的编码压缩格式;  
**Accept-Charset**：可接受的应答的字符集;  
**Host**：请求的主机名，允许多个域名同处一个IP 地址，即虚拟主机;  
**connection**：连接方式(close 或 keepalive)，如果是close的话就需要进行TCP四次挥手关闭连接，如果是keepalive，表明还能继续使用，这是HTTP1.1对1.0的新增，加快了网络传输，默认是keepalive;  
**Cookie**：存储于客户端扩展字段，向同一域名的服务端发送属于该域的cookie;  
##### 空行
最后一个请求头之后是一个空行，发送回车符和换行符，通知服务器以下不再有请求头;  
#### 请求包体
请求包体不在 GET 方法中使用，而是在POST 方法中使用。POST 方法适用于需要客户填写表单的场合。与请求包体相关的最常使用的是包体类型 Content-Type 和包体长度 Content-Length;  

### 2.1.2 响应报文
同样，先粘贴报文标准：
<div align="center">

![](http://image.wenzhihuai.com/images/20171224035755.png)

</div>

抓包，以访问([http://www.wenzhihuai.com](http://www.wenzhihuai.com))为例：
<div align="center">

![](http://image.wenzhihuai.com/images/20171224035850.png)

</div>

#### 状态行
状态行由 HTTP 协议版本字段、状态码和状态码的描述文本 3 个部分组成，他们之间使用空格隔开，描述文本一般不显示;  
● 状态码由三位数字组成，第一位数字表示响应的类型，常用的状态码有五大类如下所示：  
1xx：服务器已接收，但客户端可能仍要继续发送;  
2xx：成功;  
3xx：重定向;  
4xx：请求非法，或者请求不可达;  
5xx：服务器内部错误;  
#### 响应头部：响应头可能包括：  
**Location**：Location响应报头域用于重定向接受者到一个新的位置。例如：客户端所请求的页面已不存在原先的位置，为了让客户端重定向到这个页面新的位置，服务器端可以发回Location响应报头后使用重定向语句，让客户端去访问新的域名所对应的服务器上的资源;    
**Server**：Server 响应报头域包含了服务器用来处理请求的软件信息及其版本。它和 User-Agent 请求报头域是相对应的，前者发送服务器端软件的信息，后者发送客户端软件(浏览器)和操作系统的信息。    
**Vary**：指示不可缓存的请求头列表;    
**Connection**：连接方式，这个跟rquest的类似。  
**空行**：最后一个响应头部之后是一个空行，发送回车符和换行符，通知服务器以下不再有响应头部。  
**响应包体**：服务器返回给客户端的文本信息;

### 2.2 HTTP特性
HTTP的主要特点主要能概括如下：
#### 2.2.1 无状态性
即，当客户端访问完一次服务器再次访问的时候，服务器是无法知道这个客户端之前是否已经访问过了。优点是不需要先前的信息，能够更快的应答，缺点是每次连接传送的数据量增大。这种做法不利于信息的交互，随后，Cookie和Session就应运而生，至于它俩有什么区别，可以看看[COOKIE和SESSION有什么区别？
](https://www.zhihu.com/question/19786827)。 

#### 2.2.2 持久连接
HTTP1.1 使用持久连接keepalive，所谓持久连接，就是服务器在发送响应后仍然在一段时间内保持这条连接，允许在同一个连接中存在多次数据请求和响应，即在持久连接情况下，服务器在发送完响应后并不关闭TCP连接，客户端可以通过这个连接继续请求其他对象。
#### 2.2.3 其他
支持客户/服务器模式、简单快速（请求方法简单Get和POST）、灵活（数据对象任意）

### 2.3 影响HTTP的因素
影响HTTP请求的因素：
1. 带宽
好像只要上网这个因素是一直都有的。。。即使再快的网络，也会有偶尔断网的时候。。。
2. 延迟
（1）浏览器阻塞
一个浏览器对于同一个域名，**同时**只能有4个链接（根据不同浏览器），如果超了后面的会被阻塞。常用浏览器看下图：
<div align="center">

![](http://image.wenzhihuai.com/images/20171224043047.png)

</div>

（2）DNS查询
浏览器建立连接是需要知道服务器的IP的，DNS用来将域名解析为IP地址，这个可以通过刷新DNS缓存来加快速度。

（3）建立连接
由之前第一章的就可以看出，HTTP是基于TCP协议的，即使网络、浏览器再快也要进行TCP的三次握手，在高延迟的场景下影响比较明显，慢启动则对文件请求影响较大。

### 2.4 缺陷
1. 耗时：传输数据每次都要建立连接；
2. 不安全：HTTP是明文传输的，只要在路由器或者交换机上截取，所有东西（账号密码）都是可见的；
3. Header内容过大：通常，客户端的请求header变化较小，但是每次都要携带大量的header信息，导致传输成本增大；
4. keepalive压力过大：持久连接虽然有一点的优点，但同时也会给服务器造成大量的性能压力，特别是传输图片的时候。

BTW：明文传输有多危险，可以去试试，下面是某个政府网站，采用wireshark抓包，身份证、电话号码、住址什么的全暴露出来，所以，，，只要在路由器做点小动作，你的信息是全部能拿得到的，毕竟政府。。。
<div align="center">

![](http://image.wenzhihuai.com/images/20171224044825.png)

</div>



## 三、HTTPS
由于HTTP报文的不安全性，网景在1994年就创建了HTTPS，并用在浏览器中。最初HTTPS是和SSL一起使用，然后演化为TLS，
### 3.1 SSL


### 3.2 TLS


### 3.2 SPDY


### 3.3 HTTPS

[淘宝HTTPS探索](http://velocity.oreilly.com.cn/2015/ppts/lizhenyu.pdf)




## 四、HTTP2.0







参考：  
1. [HTTPS那些事](https://www.guokr.com/post/114121)
2. [如何搭建一个HTTP2.0的网站](https://zhuanlan.zhihu.com/p/25935872)
3. [HTTP/2.0 相比1.0有哪些重大改进？](https://www.zhihu.com/question/34074946)
4. [HTTP2.0 demo](https://http2.akamai.com/demo)
5. [Http、Https、Http2前身](https://www.cnblogs.com/wujiaolong/p/5172e1f7e9924644172b64cb2c41fc58.html)
6. [HTTP报文](http://network.51cto.com/art/201501/464513.htm)
7. [HTTP、HTTP2.0、SPDY、HTTPS 你应该知道的一些事
](https://www.cnblogs.com/wujiaolong/p/5172e1f7e9924644172b64cb2c41fc58.html)