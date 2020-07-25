# NSQ笔记

### Nsq服务端 <br>
整个Nsq服务包含三个主要部分(三大组件) <br>

### nsqlookupd <br>
nsqlookupd是守护进程负责管理拓扑信息。客户端通过查询 nsqlookupd 来发现指定话题（topic）的生产者，并且 nsqd 节点广播话题（topic）和通道（channel）信息 <br>

简单的说nsqlookupd就是中心管理服务，它使用tcp(默认端口4160)管理nsqd服务，使用http(默认端口4161)管理nsqadmin服务。同时为客户端提供查询功能 <br>

总的来说，nsqlookupd具有以下功能或特性 <br>

唯一性，在一个Nsq服务中只有一个nsqlookupd服务。当然也可以在集群中部署多个nsqlookupd，但它们之间是没有关联的 <br>

去中心化，即使nsqlookupd崩溃，也会不影响正在运行的nsqd服务 <br>

充当nsqd和naqadmin信息交互的中间件 <br>

提供一个http查询服务，给客户端定时更新nsqd的地址目录  <br>

### nsqadmin <br>
总的来说，nsqadmin具有以下功能或特性 <br>

提供一个对topic和channel统一管理的操作界面以及各种实时监控数据的展示，界面设计的很简洁，操作也很简单 <br>

展示所有message的数量 <br>

能够在后台创建topic和channel <br>

nsqadmin的所有功能都必须依赖于nsqlookupd，nsqadmin只是向nsqlookupd传递用户操作并展示来自nsqlookupd的数据 <br>

nsqadmin默认的访问地址是http://127.0.0.1:4171/  <br>

### nsqd <br>
nsqd 是一个守护进程，负责接收，排队，投递消息给客户端 <br>

简单的说，真正干活的就是这个服务，它主要负责message的收发，队列的维护。nsqd会默认监听一个tcp端口(4150)和一个http端口(4151)以及一个可选的https端口 <br>

对订阅了同一个topic，同一个channel的消费者使用负载均衡策略（不是轮询） <br>

只要channel存在，即使没有该channel的消费者，也会将生产者的message缓存到队列中（注意消息的过期处理） <br>

保证队列中的message至少会被消费一次，即使nsqd退出，也会将队列中的消息暂存磁盘上(结束进程等意外情况除外) <br>

限定内存占用，能够配置nsqd中每个channel队列在内存中缓存的message数量，一旦超出，message将被缓存到磁盘中 <br>

topic，channel一旦建立，将会一直存在，要及时在管理台或者用代码清除无效的topic和channel，避免资源的浪费 <br>

这是官方的图，第一个channel(meteics)因为有多个消费者，所以触发了负载均衡机制。后面两个channel由于没有消费者，所有的message均会被缓存在相应的队列里，直到消费者出现

### 模型动图 <br>
![image](https://github.com/GrumpyOmer/studyNsq/blob/master/images/3383256184-58ff49e3526a9_articlex.gif)

### NSQ工作模式 <br>
![image](https://github.com/GrumpyOmer/studyNsq/blob/master/images/nsq4.png)
