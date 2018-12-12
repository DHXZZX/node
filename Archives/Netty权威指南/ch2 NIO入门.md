### NIO服务端序列

1. 打开ServerSocketChannel
2. 绑定监听地址InetSocketAddress
3. 创建Selector(多路复用器)启动线程
4. 将ServerSocketChannel注册到Selector,监听
5. Selector轮询就绪的Key
6. handleAccept 处理新的客户端接入
7. 设置新建客户端连接的Socket参数
8. 向Selector注册监听读操作SelectionKey.OP_READ
9. handleRead()异步读请求消息到ByteBuffer
10. decode请求消息
11. 异步写ByteBuffer到SocketChannel