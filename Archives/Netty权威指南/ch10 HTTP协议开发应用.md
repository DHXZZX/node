# ch10 HTTP 协议开发应用

## HTTP协议介绍

HTTP是属于应用层的面向对象的协议

### HTTP请求消息(HttpRequest)

HTTP请求有三部分组成

* HTTP请求行
* HTTP消息头
* HTTP请求正文

请求行以一个方法符开头,以空格分开,后面跟着请求的URI和协议的版本,格式为:

Method Request-URI HTTP-Version CRLF

其中Method表示请求方法,Request-URI是统一资源标识符,HTTP-Version表示请求的HTTP协议版本,CRLF表示回车和换行

* GET
* POST
* HEAD
* PUT
* DELETE
* TRACE
* CONNECT
* OPTIONS

```http
GET /netty HTTP/1.1
Host: localhost:8080
Proxy-Connection: keep-alive
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36
DNT: 1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Encoding: gzip, deflate, br
Accept-Language: zh,zh-CN;q=0.9,en;q=0.8,zh-TW;q=0.7

```

### HTTP响应消息(HttpResponse)

HTTP响应由三个部分组成:行状态,消息报头,响应正文

行状态的格式为: HTTP-Version Status-Code Reason-Phrase CRLF 其中HTTP-Version为协议版本号,Status-Code为服务器返回的响应状态码,Reason-Phrase 解释Status-Code的原因

响应码由三位数字组成,第一个数字定义了响应的类别,有5种取值

1xx 指示信息.表示请求已接收,继续处理

2xx 成功

3xx 重定向

4xx 客户端错误

5xx 服务端错误

## Netty HTTP XML 协议栈开发

* 需要一套通用,高性能的XML序列化框架,它能够灵活的实现POJO-XML的互相转换,最好能够通过工具自定生成绑定关系,或者通过XML的方式配置双方的映射关系
* 作为通用的HTTP-XML协议栈,XML-POJO对象的映射关系应该非常灵活,支持命名空间和自定义标签
* 提供HTTP-XML请求消息编码器,供HTTP客户端发送请求消息自动编码使用
* 提供HTTP-XML请求消息解码器,供HTTP服务端对请求消息自动解码使用
* 提供HTTP-XML响应消息编码器,供HTTP服务端发送响应消息自动编码使用
* 提供HTTP-XML响应消息解码器,供HTTP客户端对应答消息进行自动解码使用
* 协议栈使用者不需要关心HTTP-XML的编解码,对上层业务零侵入,业务只需要对上层业务POJO对象进行编排

