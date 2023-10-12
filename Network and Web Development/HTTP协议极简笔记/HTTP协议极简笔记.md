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

# HTTP工作流程
## 非持久连接（Nonpersistent HTTP）
HTTP/1.0版本采用的连接方式，每次TCP连接后仅能通过HTTP传输一个object。我们定义RTT为客户端向服务器发出请求一直到客户端接受到服务器响应的这段时间。则在非持久连接下，每个object要花费2个RTT的时间（建立TCP + 向服务器请求object）再加上object本身传输的时间。在非持久连接下，浏览器往往需要并行多个TCP连接来提高效率。

## 持久连接（Persistent HTTP）
客户端和服务器初次建立TCP连接后即可开始持续请求和响应多个object。持久连接分为流水线式（Pipeline）和无流水线式。

### 无流水线式
客户端在请求服务器后，必须在收到服务器的回应后，才能再次发出新的请求。由于服务器在回应后往往要等待较长时间才能收到新的请求，这会导致服务器资源的闲置，两者互动的延迟也会比较高，每个请求和回应都至少需要一个RTT。

### 流水线式
HTTP/1.1版本默认采用这种模式。客户端可以持续发出请求，服务器也可一个接一个连续作出回应。多个连续的请求和回应总共花费的时间为一个RTT。

<br/><br/>



# HTTP报文格式
HTTP报文默认由ASCII码构成，大小写敏感，每行均以CRLF（即回车符和换行符）为结尾。全文共三部分：start line、header和body。
## start line
start line仅一行，请求报文和回应报文均由三个部分构成，各部分间由一个空格隔开。

### 请求报文
```
METHOD URL VERSION
```
其中，METHOD为请求的方法，URL为上文所述的资源定位符。VERSION为使用的HTTP版本。

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
header部分的每行由一对key-value构成：
```
KEY: VALUE
```
常见的KEY信息有Host（主机名）、Content-Length（传输内容的大小）、Range（指定接受object的某个部分）等。

header最后一行完全留空，用于标识header的结束。

## body
这一部分是请求的object数据本身。

<br/><br/>


# Cookies与Cache
