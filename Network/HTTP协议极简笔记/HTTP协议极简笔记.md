# HTTP简介
HTTP即HyperText Transfer Protocol（超文本传输协议），是一种应用层协议，使用TCP协议传输，采用Client/Server模式，定义了用户与服务器在Web服务中的一系列规范，包括信息如何组织和传递、客户端如何向服务器发送请求、服务器在不同请求下如何回应等。    

## URI与URL
URI即uniform resource identifier（统一资源标志符），用于在互联网上描述某个资源。URL为uniform resource locator（统一资源定位符），它用资源的位置来描述资源本身，是一种特殊的URI。用户在访问网络时，通过URL明确来告知服务器所需的资源，服务器即可作出相应的回应。

URL的主要的形式如下：
```
protocol://hostname[:port]/path/[;parameters][?query]#fragment
```
其中，protocol为协议（http、https、ftp等），hostname为主机名，一般为网站域名，也可用IP地址替代。hostname后面紧跟port（端口号），一般都会省略。再之后是path，用于标识资源在对应主机中的位置。path最终可能指向一个文件（即资源本身），也可能指向一个文件夹（此时则会指向该文件夹下的默认资源，例如default.html或index.html），当然path也可省略，此时指向的资源为对应主机设置的默认资源。

path后面是一些用于定位到资源具体某个部分的参数，在此不作展开。
<br/><br/>

# HTTP报文格式
HTTP报文默认由ASCII码构成，大小写敏感，每行均以CRLF（即回车符和换行符）为结尾。全文共三部分：start line、header和body。请求报文和回应报文均由这三个部分构成，各部分间由一个空格隔开。
## start line
start line仅一行，但在请求报文和回应报文中的格式不一样。
### 请求报文
```
METHOD URL VERSION
```
METHOD为请求的方法，URL为上文所述的资源定位符。VERSION为使用的HTTP版本。

一些常见的方法：
- GET：向服务器请求某个object
- HEAD：与GET方法类似，但是服务器的回应报文仅有header。客户端一般用HEAD方法去检测某些object是否存在或更新，而不是真的要去获得这些object。
- POST：客户端传送某些数据，让服务器存储或处理。
- PUT：HTTP/1.1版本新增。PUT与POST类似。但PUT具有幂等性，对于用户可能多余的重复操作（如抢票、下单），使用PUT方法更为保险。
- DELETE：HTTP/1.1版本新增，用于删除服务器中指定object。


### 回应报文
```
VERSION CODE STATUS
```
STATUS为状态，CODE为状态代码，不同的状态对应不同的代码。以2开头的代码表示请求正常，3开头的代码表示需要进一步操作，4开头的代码表示客户端出现问题，5开头表示服务器出现问题。

常见的状态、代码及相应描述：
| 代码 | 状态 | 描述 |
| ---  | --- | ---  |
| 200 | OK | 请求成功，回应报文中将包含所需的object |
| 301 | Moved Permanently | 请求的object被移动，回应报文中将包含新的地址|
| 400 | Bad Request | 请求报文无法被服务器理解 |
| 403 | Forbidden | 禁止访问该object |
| 404 | Not Found| 请求的object没在服务器中找到 |
| 500 |Internal Server Error| 服务器内部错误|
| 505 | HTTP Version Not Supported | 不支持该HTTP版本|

## header
header部分的每行由一个域（field，可以看作是一个键值对）构成，用来描述报文、主机或数据的一些属性：
```
KEY: VALUE
```
常见的KEY信息有Host（主机名）、Content-Length（传输内容的大小）、Range（通过字节数指定接受object的某个部分）等。

header最后一行完全留空，用于标识header的结束。

## body
这一部分是请求的object数据本身。

<br/><br/>

# HTTP常用优化
## Cookies
### 简介
HTTP是无状态协议，过去客户端与服务器进行的交互不会被记录，也无法影响未来的交互。虽然无状态协议简单高效，计算资源消耗也小，但也无法满足现在越来越多样化的Web服务。例如在论坛网站上，由于无状态协议无法记录之前的登录信息，因此每次评论都需要重新登录，这会非常麻烦。

Cookies功能解决了上述麻烦。其功能通过三个部分实现：报文header中Cookies相关的域、客户端本机的Cookies文件、服务器后端存储Cookies信息的数据库。

简单来说，Cookies就是一个用户认证机制，服务器通过Cookies识别出不同的客户端（用户）。这样一来，不仅可以免除用户反复登录的麻烦，服务器可在后台数据库中记录不同用户的个性化需求（例如用户设置、用户收藏夹、个性化推送等功能）

### Cookies工作流程
1. 客户端在首次登录某网站后，网站服务器便会为该客户端生成一个独一无二的Cookies信息（其中包括了分配给该客户端的sid、Cookies过期时间等信息）。该Cookies信息会保存在服务器的数据库中。
   
2. 服务器通过回应报文中的Set-Cookie域将Cookies信息传回给客户端。
   
3. 收到服务器生成的Cookies信息后，客户端将其保存在本机的Cookies文件中。
   
4. 客户端在Cookies有效期内再访问该网站，在请求报文中的Cookies域中加入对应的sid，服务器收到该报文后与数据库进行比对，便可知道来者何人。


## Cache
### 简介
为了进一步提高Web服务质量，减少网络流量，对于网页上比较常用的object会进行缓存（Cache）操作。客户端在访问网站时，首先会对Cache发送请求，若客户端要访问的object存在于Cache中则可直接返回该object。若没有，则Cache负责向服务器发送请求，得到对应object，缓存并返回给客户端。

Cache机制实际上不一定仅部署在客户端本地。很多ISP会将Cache机制部署在代理服务器中，从而优化旗下所有用户的体验。

### Conditional GET
如果服务器中某个object更新了，且该object有对应的Cache，客户端如何才能保证自己访问到最新的object呢？Conditional GET方法可以解决该问题。

当Cache缓存object时，同时会记录该object的缓存时间（假设为t1）。客户端每次请求该object时，Cache会向服务器发送Conditional GET的请求报文来检查该object是否被更新。请求报文使用GET方法，并在header中加入“If-modified-since”域，告知服务器该object的更新时间t1。如果服务器中的object在t1后确实被更新了（假设更新于t2），则返回最新版的object和t2，Cache则缓存最新的object并将更新时间记录为t2。如果服务器中的object没更新，则仅返回状态代码304：Not Modified。

<br/><br/>

# 改进HTTP：新版本以及HTTPS
## HTTP各版本的特性
### HTTP 1.0
HTTP 1.0是一种无状态，无连接的应用层协议，采用非持久连接（Nonpersistent HTTP）的方式。浏览器的每个请求都要与服务器先建立TCP连接，请求完成后TCP连接立刻结束（无连接）；服务器在这一过程中不跟踪、不记录任何客户端的信息（无状态）。

### HTTP 1.1

HTTP 1.1版本采用持久连接（Persistent HTTP）的方式，通过在报文中添加“Connection”字段（并将值设置为“Keep-Alive”），客户端和服务器初次建立TCP连接后即可进行多个请求和响应，避免了反复建立和销毁TCP连接，提高网络效率。

在持久连接下，HTTP 1.1还支持管道技术（Pipelining），简单来说就是客户端可以连续发送请求，而无需等待第一个请求被回应后再发第二个（具体实现上，可通过在一个TCP连接中发送多个HTTP请求报文来实现）。然而，客户端仍需要按自己的请求顺序来一一接收服务器的回复，如果某个资源的传输出了问题，仍然会阻塞客户端后面待接收的资源，这种情况称为队头阻塞（head of line blocking）。

由于HTTP 1.1的管道技术并不能避免队头阻塞问题，许多浏览器并未采用这项技术，而是通过并行多个TCP连接来实现提高浏览器的访问效率。

### HTTP 2.0
HTTP 2.0主要引入以下几种技术：

#### 二进制分帧

HTTP 2.0在应用层和传输层中间加入了二进制分帧层来传输HTTP报文。


#### 多路复用

#### 头部压缩

### HTTP 3.0
引入了基于UDP协议的QUIC协议。

## HTTPS


<br/><br/>


# 参考资料
[认识HTTP----Cookie和Session篇](https://zhuanlan.zhihu.com/p/27669892)

[快速掌握HTTP 1.0 1.1 2.0 3.0 的特点及其区别](https://zhuanlan.zhihu.com/p/266578819)