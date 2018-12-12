# ch6  编解码技术

Google Protobuf

Protobuf 全称 Google Protocol Buffers 

特点:

* 结构化数据存储格式(XML,JSON等)
* 高效的编解码性能
* 语言无关,平台无关,扩展性能好
* 官方支持Java,C++,Python三种语言

Facebook Thrift 主要组成部分

* 语言系统以及IDL编译器: 负责有用户给定的IDL文件生成相应语言的接口代码
* TProtocol: RPC的协议层,可以选择多种不同的对象序列化方式,如 JSON和Binary
* TTransport: RPC的传输层,可选择多种不同的传输层实现,如socket,NIO,MemoryBuffer等
* TProcessor: 作为协议层和用户提供的服务实现之间的纽带,负责调用服务实现的接口
* TServer: 聚合TProtocol,TTransport和TProcessor等对象