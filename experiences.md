```text
1. RPC 调用失败重试机制
若 RPC 接口调用失败，可将回调内容推送到 RabbitMQ 进行重试。

2. 防止 worker 并发或资源独占
将 worker 按用户ID进行分组，特定用户只使用一个固定队列即可。 

3. 阿里云的 SLB 空闲 TCP 存活时间最长为 900 秒（15分钟）
如果 worker 的工作时间超过 15 分钟，连接就会被 SLB 关闭，导致 worker 无法对该消息执行 ack 操作。
可以通过设置 `no_ack = true` 避开，为避免消息丢失，在程序出现异常后需将消息重试加入队列。

4. Linux 的 TCP 连接若超过 2 小时空闲，则会启动心跳检测
这有利于将半打开或无效的连接关闭，回收资源
客户端和服务端也可以实现类似 keepalived 的功能，比如 RabbitMQ 的 heartbeat 心跳检测
能更加灵活地配置心跳检测的时间间隔

5. 当你发生某个 Library 不知道怎么使用时，而且又缺少文档，可在项目的 tests 中寻找答案。
```