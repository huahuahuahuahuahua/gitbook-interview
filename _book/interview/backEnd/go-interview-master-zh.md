## 一家做智慧社区的公司：

## 一面
  0.  函数传递是什么传递
  0.  new & make的 内存分配原理
  0.  slice 和 array的区别 以及内存原理
  0.  structA 和 structB 如何判断是否相等
  0.  interface在内存中的实现
  0.  GC
  0.  MySQL innodb索引中， 查询非主键索引时，数据是如何索引到的

## 二面
  0.  一个slice里有X个数，需要写一个算法取出出现次数最多的Top N
  0.  双向链表的实现
  0.  异常可否跨协程捕获
  0.  一张极大的mysql表如何在业务热更的前提下拆表，怎么拆，每一步怎么做
  0.  微服务跨库事务的实现及分布式事务锁的抢占和释放

## 三面
  0.  kafka的zero copy是怎么实现的
  0.  kafka的高可用和高性能是如何做的
  0.  是否用过流式计算， 用到什么技术栈
  0.  聊一个有技术含量的且重度参与的项目或模块，讲讲业务实现和技术点




## 补充：
1. docker部署和K8s
2. 慢查询怎么解决 
3. mysql突然CPU高是怎么回事儿， 连接池没满， 也没有慢查询
4. go突然内存满了，怎么找问题。
## Go-成都3年社招-20210419.md

### 技术 1M

* 自我介绍

* 使用beego 和 gin 后谈谈对两者的感受？

* redis 有哪些数据类型，可以用在哪些场景？如何保证高可用性？redis 分布式锁用什么命令，应该注意些什么问题？

* golang 中 cap 的作用，可以用在哪些数据类型上？

* MySQL 索引优化

* golang 内存逃逸分析

* golang GMP 调度原理

* golang GC 

* golang sync 包中互斥锁、读写锁、waitgroup 介绍

* url 过长导致二维码像素点过密，怎么解决？

* 项目中发号器用什么实现的？

* 给你一个秒杀场景你如何实现？

* 用过延时队列吗？如果不适用 mq、redis等自己怎么实现一个延时队列？

* 讲讲 堆 数据结构什么样的？ 再讲讲 trie（字典树）

* （因为简历中我有写到敏捷开发）在敏捷开发中担任什么角色？怎么开展这一角色的工作？

### HR 2M

* 为什么离开现在的公司？

* 来我司前面试了多少家公司？如果今天面试觉得公司不错还会继续面试综合考察还是停止面试？

### 研发经理 3M

* 谈谈个人未来的职业规划

* 谈谈我现在公司的技术架构？微服务怎么拆分的？
### 一面
1. 之字层序打印二叉树，
2. go 调度，锁实现，channel 实现，其他相关 go 基础。
3. mysql 加锁分析，隔离级别，原子性，等常见问题。
4. redis 基础结构，内部实现，日常优化，热点数据，大 key
5. 简历项目，难点亮点挑战性

### 二面

三数和，简历项目，难点亮点挑战性

三面:

跳槽原因，职业规划，简历项目，挑战与亮点，系统设计: 实现抢红包功能# 一面

自我介绍

算法题：

1. [https://leetcode-cn.com/problems/find-peak-element/](https://leetcode-cn.com/problems/find-peak-element/)
2. [https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)
3. [https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

索引，倒排索引，切词，如何根据 doc id 有没有出现某个 token

服务高可用是怎么做的

MySQL 可重复读、读提交区别、原理

爬虫 URL 去重，设计存储结构（FST，前缀树+后缀树）

MySQL (a,b,c) 索引，几条 SQL 走索引的情况

思考题：

概率 p 生成 0，1-p 生成 1，如何 1/2 概率生成 1

# 二面

算法题：

1. [https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)
2. [https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)
3. [https://leetcode-cn.com/problems/coin-change-2/](https://leetcode-cn.com/problems/coin-change-2/)

讲一下 es 索引的过程

切词怎么切，切词算法，降噪

让你带应届生，怎么带，有什么工程经验可以分享

Redis 缓存淘汰有哪些

# 三面

自我介绍

算法题：

1. [https://leetcode-cn.com/problems/first-missing-positive/](https://leetcode-cn.com/problems/first-missing-positive/)

文章下面的评论，按点赞数排序，SQL 怎么写

把所有评论放到内存里，怎么设计数据结构，存储并排序

`select *` 会有什么问题

缓存热 key 怎么解决

职业发展

领导如何评价你

项目难点，亮点## 个人情况
毕业两年，北京某在线教育，技术氛围不错。主要语言栈golang、python，主要技术方向是k8s、容器、云计算。有服务上云的实践经历，了解cicd基本流程，求知意向是容器研发、基础架构研发、运维研发之类的（主要还是研发方向）。

## 面试周期
2021.7.19 ~ 2021.8.30

## 已面试公司
快手、字节、小米、金山云、完美世界、京东云、百度、西安腾讯云。
快手二面卒，字节一面卒（面了两次，约了第三次二面过了拒了三面），小米二面卒，金山云感觉聊的挺好的一面卒，完美世界offer，京东云现场面offer，百度一面卒，西安腾讯云offer。

面试前期准备很重要！！！准备充分面试时感受会好很多。千万别裸面！！！

## 简单说一下面试的基本问题吧（主要还是偏向基础）
### 项目方向：
项目的话我不多说什么，就是自己的项目细节自己肯定清楚，如果项目中不是自己做的部分，建议不要在简历上写太多，写清楚自己做了什么，容易被抠细节问，项目一般都会抠细节，特别细的那种！！！

### 语言栈：
因为我主要语言栈是go，所以一般都比较少问python。
#### golang
1、gin框架路由怎么实现的，具体正则怎么匹配？限流中间件怎么实现？
2、go的slice与数组的区别，slice的实现原理，源码？
3、golang的协程调度，gpm模型。协程调度过程中的锁。
4、golang的channel实现，channel有缓存和无缓存，一般会直接撸码（三个goroutine顺序打印）。
5、golang的关键字defer、recover、pannic之类的实现原理。
6、sync包里面的锁、原子操作、waitgroup之类的。
7、make和new的区别，引用类型和非引用类型，值传递之类的。
#### python
1、python多线程、多进程。
2、python的装饰器怎么实现的？

### 操作系统：
1、进程、线程、协程间的区别以及他们间的切换之类的，有时候会问到语言级别的协程。
2、io复用、用户态/内核态转换
3、awk命令
4、linux查看端口占用
5、top命令，free命令中的各个参数表示什么，buff/cache都表示什么？

### k8s & 容器：
1、简单聊一下什么是云原生、什么是k8s、容器，容器与虚机相比优势。
2、k8s组件，pod创建的过程，operator是什么？
3、docker是怎么实现的，底层基石namespace和cgroup。
4、k8s的workload类型，使用场景，statefulset你们是怎么用的？
5、limit和request，探针，一般怎么排查pod问题，查看上次失败的pod日志。
6、sidecar是什么，怎么实现的？
7、pv，pvc，动态pv怎么实现
8、k8s的声明式api怎么实现的，informar源码。
9、cicd，发布模式。
10、svc的负载均衡、服务发现，ipvs与iptables。
以上基本是会被问的点（虽然有一些问题我也不是很熟），另外很多会被问k8s的网络之类的，因为我比较菜，这块被问的比较少。

### 计算机网络：
1、tcp三次握手四次挥手，为什么不能是两次握手，三次挥手？握手和挥手过程中的状态。
2、time_wait作用，为什么是2msl，close_wait作用，time_wait过多怎么办？
3、http请求的过程，浏览器输入网址请求过程？dns解析的详细过程？
4、https与http的区别，https第一次服务端回传是否加密？
5、tcp与udp区别，tcp怎么保证可靠性。
6、http请求头、分隔符、长连接怎么实现

### 数据库：
1、mysql的事务，事务使用场景。
2、mysql的索引，什么情况下索引失效，聚簇索引与非聚簇索引，索引的存储b+树与b-树区别。
3、join的内外连接，最左匹配原则。
4、redis的数据结构，hmap怎么实现的，持久化怎么做，go操作redis的方式。
数据库方向有被问到，我基本没答上来（一般都告诉他只会基础，开发直接使用gorm）。

### 数据结构与算法：
1、倒排索引和B+树
2、判断链表是否有环，时间复杂度要求0(1)
3、LeetCode上合并区间的题
4、leetcode的股票买卖的题
5、二叉树的最近公共祖先
6、有序数组合并
7、什么是平衡二叉树、最小堆
8、大文件的top10问题
9、golang实现栈、队列

### 其他：
1、git 的相关操作，合并commit，合并之类的。
2、场景设计（比较多）
# live.me (出海直播) (一面)

1. 讲一下GMP模型,goroutine是如何被调度的
2. 是否遇到标准库的包的性能问题,怎么解决的
3. goroutine的泄漏有哪些场景
4. 线程和协程的区别
5. slice和array的区别
6. 程序内的错误处理
7. error和panic的区别
8. 接口出现延迟,怎么查问题
9. mysql的存储引擎有哪些，为什么选innodb
10. mysql的索引存储结构
11. 联合索引abc,select * from t where a=?，b=？，c>会命中(a,b,c)吗，执行流程,有几次io操作
12. 大表分页查询有什么问题
13. mysql主从同步慢一般是什么原因,如何避免
14. redis的数据类型
15. 积分排行使用啥结构
16. zset的底层是什么存储结构
17. 更新缓存，怎么处理
18. 缓存和DB一致性怎么处理
19. 介绍一下分布式锁
20. redis实现锁有可能有什么问题
21. 分布式锁超时时间少于业务执行时间怎么处理
22. 微服务间的调用需要考虑哪些方面
23. 设计一个feeds流，表设计，数据写入流程
24. 设计系统的时候，高并发，高可用从哪些方面进行考虑## 小米面试

### 一面

1. innodb MVCC实现
2. b+树是怎么组织数据的，数据的顺序一定是从左到右递增的么
3. 页分裂伪代码，b+树的倒数底层层可以页分裂么
4. 合并k个有序链表
5. redis的hashtable是怎么扩容的
6. select poll epoll，epoll具体是怎么实现的
7. GMP是怎么调度，channel是怎么收发消息的，channel的recq和g是怎么建立关系的
8. innodb二次写是什么
9. undo里面具体存的是什么
10. b+树节点具体存的是什么
11. mysql一页最大能存多少数据
12. myisam和innodb索引上的区别
13. innodb commit之前，redo 的prepare然后binlog commit，然后redo再commit有什么缺点？5.6之后是怎么优化的？
14. redo和binlog的区别
15. 读锁和写锁区别

### 二面

1. 蛇形打印二叉树
2. myisam为什么不支持事务，如果要支持事务要怎么做
3. 函数只能返回1-7的随机数，请用这个函数返回1-5，要求平均
4. 聊项目

### 三面

1. go的协程调度和os的线程调度有什么区别
2. 只有写锁实现读写锁
3. go的调度是怎么实现的
4. go的网络IO为什么快？还有优化空间么
5. epoll为什么这么快，还有优化空间么？如果要你实现一个网络IO应该怎么实现
6. 设计一个每秒80万qps的过滤器
7. 过滤器用redis实现，宕机期间数据怎么恢复
8. 设计一个下单 扣减库存的分布式应用，请求超时了怎么办，一直重试超时了怎么办
9. 数组A1 2和数组B2 3是一个关系圈，A能通过2找到3，数组A1 2和数组B2 3和数组C 3 5也是一个关系圈，给一个二维数组求关系数
  ##小米游戏
    ###一、 介绍连接池项目
   1. 介绍连接池常用的参数，最大连接数，最小存活数这些意义，为什么要有这些
   2. 当链接超过最大连接数怎么处理，等待有空闲连接还是创建一个继续给出，比较两者的优劣
   3. 连接池清理链接的逻辑，如何优化的
   4. 当连接池中有一些链接不可用了怎么办，如何保证这些连接的可用
   5. 当出现下游某个实例挂掉了，连接池应该怎么处理
   6. 对比 mysql redis http 连接池的实现
      ###二、 介绍负载均衡算法
   1. 介绍平滑负载均衡算法，实现
   3. 当出现下游出现不可用，负载均衡算法怎么处理
      ###三、 介绍聊天室项目
   1. 介绍实现原理的，互相通信的逻辑
   2. 聊天室服务端如何把消息下发给用户
   3. 介绍websocket包的字段
   4. 当有用户掉线怎么处理
      ###四、 redis相关
   1. redis的数据结构
   2. 各个数据结构的操作( 这个忘了，不会… )
   3. 各个数据结构的使用场景
   4. 如何保证 Redis 的高可用
   5. 当有一个key读取的频率非常高怎么办 ( 这个也是不会)
      ###五、 算法相关
  1. 介绍快速排序
优先队列的实现 ( 没有答好 )

### 映客直播(一面)

1. map源码
2. go内存分配
3. new和make的区别
4. mysql的聚簇索引和非聚簇索引
5. redis是单线程的吗
6. redis底层网络原理
7. tcp的拥塞控制
8. tcp协议栈中TIME_WAIT字段的作用
9. etcd raft协议
10. etcd 选主原理
11. trace了解吗
12. 接口超时如何快速定位问题
13. 网络故障导致的订单残数据缺怎么恢复
14.支付接口幂等性怎么做
15.操作系统了解吗
16.linux load值怎么计算

### 映客直播(二面)


1. mysql超时是什么引起的
2. redis超时是什么引起的
3. 接口超时怎么定位问题
4. 如何取关注主播的前100个用户
5. 分表的场景如何查询主播下面的用户和用户下面的主播 
6. 如何设计一个秒杀系统
### 游族网络(一面)

1. runtime调度原理
2. GC原理
3. map源码
4. context使用场景
5. mysql索引原理
6. Redis持久化原理
7. kafka使用场景

### 游族网络(二面)
1. 游戏录像怎么实现
2. rank系统怎么设计
3. 实时战力前100排行榜怎么设计
  发现我在ACM中未获得奖项 且算法了解没那么深入，蓝桥杯省赛成绩没那么ok( 备注：省赛题目一般都很简单，属于课后习题的变种)   整体没有展现出优势。
  算法考察：变种二分查找 ok、爬山fail
  基础：TCP 三次握手四次挥手、 timeWait  怎么产生的；TCP UDP区别、 拥塞控制、 UDP丢包重传的设计思路。
  linux 平常命令、TOP实现、 Epoll 实现，红黑树存储描述符
  操作系统调度算法 先来先服务、短作业优先、了解
  系统设计： 1、让你设计一次从服务云端下载一个大文件到本地，如何实现？ 引导UDP重传思路。 double 报文回传3次。
  2、对DNS 服务如何设计、资源协作分配、  延伸拓展性ok。
  3、两个大文件的相似度识别、向量-关键词、 这块了解的不太ok


###滴滴  度小满  字节  360  小米  微博  唱吧 合集

## 一.PHP面试题
1. PHP GC
2. PHP生命周期
3. php-fpm  nginx  cgi, 请求执行过程描述
4. PHP array底层实现
5. 10个php数组函数
6. php自动加载函数实现
7. 实现类似var_dump的函数
8. 如何查看fpm进程挂掉的原因
9.f pm怎么配？静态动态进程池，个数，请求过多会怎样？
10. php7特性有哪些？
11. php PUT 请求参数如何获取？

## 二.java（投的java较少）
1. java GC
2. cas
3. hashtable  hashmap区别

## 三.网络
1. 三次握手
2. 状态码列举，502  504区别  301  302区别
3. http2  http3有了解吗
4. http安全问题有哪些，https解决了哪些问题
5. https整个流程描述
6. http header有哪些部分？
7. tcp udp区别
8. session  cookie区别，分别存哪里？跨域问题如何解决
9. rpc和http优劣
10. 一个http请求到响应全过程描述
11. 长连接 http长连接和websocket对比
12. 长轮询和短轮询了解吗

## 四.操作系统和nginx
1. Nginx—进程模型
2. nginx负载均衡方案，一致性哈希描述，一致性哈希和轮询对比
3. nginx I/O模型
4. epoll select描述  
5. 进程间通信方式


## 五.Mysql
1. 索引区别  Innodb和MyIsam
2. 如果Innodb没主键怎么办？
3. 聚集索引和非聚集索引区别
4. mysql有哪些日志？binlog日志格式
5. 联合索引怎么存储？
6. 覆盖索引相关问题 
7. 自动生成主键和插入主键哪个快？
8. mysql有哪些锁
9. 死锁怎么产生的，如何避免？
10. 脏读  幻读概念，怎么解决。
12. 事务隔离级别，事务特性描述
13. 间隙锁坏处，如何避免
14. ACID分别是如何保证的
15. 写一条sql统计， 统计当天访问量前10的ip
ip  visit_time   url
102.12.12.1   2020-03-25 10:10:10   www.baidu.com

16. mysql主从同步延迟问题如何解决，主库宕机数据如何防丢失？
17. 分布式事务了解
18. 主从复制过程描述
19. 唯一索引实现

## 六、redis
1. redis数据类型及底层结构
2. redis过期机制
3. redis rehash机制 冲突解决。
4. 集群平滑上下线—一致性哈希
5. redis -- 雪崩后卡的原因
6. redis mysql低延迟高一致性解决方案--如何保证缓存与数据库读写一致性
7. Redis--事件模型
8. redis持久化机制，RDB和AOF区别，AOF重写
9. redis内存淘汰策略
10. redis主从复制过程
11. redis哨兵选leader过程、槽相关、redis-cluster和codis扩展
12. setnx,  set nx ex, redlock（甚至zk）等  分布式锁相关，可能遇到哪些问题及解决方案
13. redis incr实现
14. redis大key会有什么问题，如何解决


## 七、其他+海量数据处理问题
1. 列举你了解到的mq中间件，找出两个对比特点
2. sso设计，sso跨域问题 
3. jwt
4. oauth 2.0
5. 设计秒杀场景
6. 设计8位唯一数字id，有效期7天
7. 卡券系统--资损解决
8. 上线的场景
堡垒机  A
线上  B C D E F G 6台

任意两台机器的带宽是10M
A机器有10M数据量，最短需要多少时间完成上线？

9. 亿级数据，每一行两列，如何按照第一列升序 第二列降序排列？数据分布不均或较为集中的情况又如何处理更好？
10. 几十亿行数据，记录当天的抖音用户  uid  登录时间  登出时间
如何计算出日活峰值在哪个时间（精确到秒），以及持续了几秒。

11. 一盏灯过河问题  
ABCD四人过河时间分别是 1 2 5 10分钟，每次最多俩人过河，且必须拿灯过河。最短需要几分钟？


​             

## 四.数据结构和算法、智力题
1. B树  B+树 红黑树 跳表描述
2. leetcode岛屿问题
3. 最大子序列和
4. 最长无重复子串
5. 26进制字母求和
6. 取单链表的倒数第K个结点
7. 合并区间
8. LRU实现--针对hash存储的方式，如何改进？
### 滴滴(一面)

1. 算法题:twosum
2. mysqlB+Tree
3. mysql索引类型
4. mysql事务隔离级别
5. 分库分表扩容怎么减少影响
6. cookie和session
7. redis持久化方式
8. http错误码有哪些
9. 500有哪些,什么原因
10. nginx反向代理怎么配置
11. nginx平滑重启什么原理
滴滴（一面）

1.介绍项目

2.对了，问我为什么选择GO，看我有Java从业经历。

3.介绍一下java 和 go 区别，我猜是让我说一些他们的不同点，go 比java 哪里好。我说了一些特性

3.1 问我协程比进程好在哪里？ 我自己顺便说了进程线程 协程三者关系

4.问我想从事什么 

5.项目中有bloom 介绍了一下怎么使用的，精度，损失

6.GPM模型 知道啥说啥

7.redis使用模式 主从 哨兵啥的 我把特性什么的知道的都说了一下

8.接着聊项目，然后问了算法 我忘记问啥了

渐进式的聊面试，很轻松

10.问我能不能接受看php？

反问时候

聊了一下GORM，应用情况。他们的go-spring，还有他们的夜莺系统。因为看过一点点所以想问问

有培养体系，教我如何写GO（这个我很欣慰），说有大佬内部课程

二面

1.自我介绍（面试官也不看我，一脸严肃我特害怕。然后自我介绍磕磕绊绊的）

2.问我看源码吗？

3.问了问GC 发展史，都怎么玩的 每次优化了啥

4.问了问我go 内存 优化了那些东西（这题我忘了咋问的了）

5.问了一下我项目里nodejs 升级为 java 为啥会快了那么多。

6.问了红黑树特性，哪个数据结构用到了。我介绍了一下 红黑树 一些特性 比如 平均查找时间低 插入删除需要 左旋右旋调平衡。 我想到 java里 hashmap 用到了这个结构

7.问了一下map的底层结构   顺便介绍了一下 sync map

8.找出两个大文件交集

9.算法 leetcode  两棵树 b 是 a子集那道题思路 怎么做 dfs 然后比较 值和 指针

10.聊了一下 我的项目 召回相关的 和 nodejs java 迁移 效率提升问题

反问

聊了一下 go-spring 夜莺 还有 didi 有个 写sql的github 项目 想问一下应用情况。问我能不能接受 看看php 之类的

问了问我为啥离职
###1. go语言
   1. Context 的使用，用法，底层实现
   2. Slice array 的区别，Slice 的底层实现，扩容规则
   3.  聊到了内存对齐，说说为什么要内存对齐，原理原因
   4. Chan 的底层实现，用法，什么时候会panic
   5. Map 的底层实现 sync.Map 的底层实现
###2. Mysql
   1. 说说四个隔离级别，对应的读
   2. Innodb 默认是哪个
   3. 说说脏读，幻读，不可重复读
   4. 说说 innodb 如何避免各种读的
   5. 说说 mvcc 的底层实现
   6. 说说这些间隙锁的底层实现 ( 我真的学不动了 )
   7. 说说 innodb myisam 的区别
   8. 说说 主键索引和非主键索引底层实现的区别
  9. 联合索引的实现
  10. 对 a b c 建索引，找 b c 时会不会走索引？ 精准找 a 范围 b 精准 c 呢？
 ###3. Redis
  1. 说说 redis 的淘汰算法
   2. 说说 LRU 算法
   3. 说说数据结构
   4. 说说 zset 的底层实现
   5. 说说 为什么用跳表 而不是红黑树 二叉树
   6. 说说 redis 中到期删除是怎么实现的
## 美团A部⻔门⾯面试过程汇总
⾯试⽅式: ⽜客⽹视频链接，因为可以⼿撸代码 ⼀般职级越⾼，技术⾯试轮次越多

3-1以下应该是三轮技术⾯

3-1通常四轮技术⾯，⼀、⼆轮技术和项⽬负责⼈⾯，三、四轮是交叉⾯或部⻔

领导⾯，五⾯是HRBP，六⾯专职HR谈薪⽔收到offer后接受开始背调，⼀般需要5个⼯作⽇背调结果给到公司，联系⼈及⼯作时间必须准确⽆误。
3-2以上据⾄少五轮技术⾯，没这待遇啊。⼤体这样，当然每个部⻔安排顺序不⼀定相同。因为是两个部⻔都做了⾯试，⾯试官⻛格也不⼤⼀样。

###【美团A部门一面技术】
1. JVM如何优化的？CMS算法执⾏流程？什么情况下发⽣的Full GC？
2. 分布式事务讲⼀下？结合项⽬想讲的可靠消息⼀致性实现⽅案 + 最⼤努⼒送达通知⽅案，最后也提到了单应⽤多DB（JPA）、TCC事务以及适⽤场景。
3. ES是如何调优的？副本机制作⽤？写⼊doc操作执⾏过程？
4. 其他也都是基础问题，印象不是很深刻了。


###【美团A部门二面技术】
1. 将最近做的两个项⽬详细讲解 + 现场画出整体系统架构图并结合架构图讲解+ 设计模式类图
我是拿了⼀张纸，画出来然后对着电脑屏幕开讲，这⾯试画⻛可以想象⼀下哈，⾯试官隔着屏幕看。
2. 因聊的项⽬时间⽐较⻓，也因为电脑快没电了，所以接下来⼏道技术题他出完我很快回答出来了。
3. MySQL数据库底层实现结构？B+树结构，也讲了数据⻚，以及⻚⽬录相关的
4. Spring Bean循环依赖如何解决的？
5 .MyBatis⼆级缓存如何实现的？
6. 有个项⽬⽤的分布式缓存是CouchBase，要求讲下CouchBase基本原理？
7. RabbitMQ如何保证⾼可⽤的？queue数据在节点之间如何同步的？死信队列
如何实现的？

技术回答的很快，最后丢⼀句「等HR电话」，我是有点懵逼，难道都该聊到hr
⾯了，这不才2⾯技术⾯么。
因为电脑⻢上没电了，没有多问，已经聊了⼀个半⼩时了。
后来才知道，是等HR电话通知我约下⼀个⾯试官啊。。。果然是好久没⾯试
了^_^


###【美团A部门三面技术】
 讲解了项⽬的从客户端到后端的具体流程。主要问了⼀些扩展技术⾯，⽐如Http2都有哪些改进的？Redis最新特性了解哪些？等等吧，总体也不是很难的。


###【美团A部门四面技术】
1. ⾃我介绍、介绍具体项⽬突出贡献
2. 离职原因？业务⽅向如何考虑的？ 职业规划？
3. ⾼并发与性能的关系？根据项⽬经验⾃有发挥吧，但尽量还是答案宏观⼀点。
4. 缓存和数据库⼀致性如何保证的？谈到了分布式锁，那详细讲讲分布式锁实现？redis setnx、redisson、zookeeper
5. 项⽬架构中如何做技术选型？
6. 有什么要问我的吗？

###【美团A部门五⾯面HRBP】
1. 为什么要离职，基于什么原因考虑的？
2. 你的未来职业规划是什么样的？项⽬中的⻆⾊是什么样的？
3. 你认为⾃⼰还有哪些需要提升的吗？
4. 你在团队中的绩效如何？
5. 有什么需要问我的吗？
##美团B部⻔门⾯面试过程汇总
###【美团B部门一⾯ 技术】
1. JVM中的CMS和G1垃圾回收算法具体区别?
2. 问了两道线程池相关问题，觉得没啥问题了 此时，话锋⼀转，咱们聊聊中间件相关吧 【后来听说，⼈家是有个⾯试⼩模板^+^ 】

接下来开始问中间件相关:

1. Dubbo
    1.1 说⼀下Dubbo的具体执⾏流程，涉及哪些模块，作⽤？
    1.2 使⽤过过Dubbo哪些特性，做过哪些扩展？Dubbo⾥的泛化有了解过吗？
    1.3 Dubbo中的注册中⼼Zookeeper是如何注册上去的？
2. Zookeeper
    2.1 Zookeeper节点有哪⼏种类型（临时、持久）？
    2.2 如何注册的，如何选举的（选举算法？），
    2.3 如何完成监听或者说是订阅的？
    2.4 提到了分布式锁，在Curator框架中是如何实现的，watch机制本地数据结构啥样的？
3. Redis
    3.1 都⽤过哪些数据类型？分别介绍下使⽤场景？
    3.2 持久化机制，AOF、RDB具体区别有哪些？
    3.3 Redis 主从同步机制是怎么样的，⽐如slave启动之后同步过程？
    3.4 Redis Cluster集群如何选主的？
    3.5 Redis Cluster 跟哨兵模式有什么区别吗？
    3.6 Sentinel 哨兵模式是如何选主的？这⾥说跟cluster差不多，追问了下，其实还是有些区别的， sdown odown 主观宕机、客观宕机⽅式不太⼀样
4. Kafka
    4.1 ⽣产端是如何发送⼀条消息到Broker的？
    4.2 具体可以调整哪些参数提升吞吐量？
    4.3 消费端发⽣rebalance的过程是怎样的？⽐如有⼀个新的consumer加⼊
    到了Group中是个什么流程？
5. ⼿写算法
 算法题：⼀个链表，输⼊k，⽐如k=3，翻转前3个链表值

### 美团B部门二⾯技术】
1. 都是发散探讨性的题⽬⽐如⽤过CouchBase，那你觉得CouchBase有哪些不⾜的地⽅，哪些不够专注的？谈到专注，我也提到了Redis不够专注，因为Redis5.0还提供了Stream作为持久化队列解决⽅案，Redis应该更专注分布式缓存这块，反⽽野⼼也很⼤，还要⽀持类似MQ的功能，并且也参考了Kafka设计思想，所以也不够专注。

2. ⼿写多线程题⽬：T1线程输出都是A，T2线程输出的都是B，T3线程输出的都是C要求三个线程启动后输出顺序：ABCABCABC
 【技术⼆⾯⽼哥是清华的，京东项⽬负责⼈也是清华的，他们两个⾯都有⼀个共同特点，就是题⽬都是⽐较发散的，就是跟你来探讨技术以及实现，由浅⼊深，看看你是如何思考的，为什么这么来设计？有没有什么更好的解决⽅案？】

###【美团B部门三⾯ 技术】
部⻔总监⾯，主要聊项⽬，也聊到了Reactor模式。因为已确定去另外⼀个部⻔，没有安排交叉⾯，或者已经有过交叉⾯了，如果决定去这个部⻔也不⼀定会再重新安排了，
交叉⾯⾯试官也不是很好约⾯的，其主要作⽤就是把把关⽽已，其决定作⽤的还是本部⻔的⾯试。


# 一面

- 算法题二选一
    - [https://leetcode-cn.com/problems/permutations/](https://leetcode-cn.com/problems/permutations/)
    - [https://leetcode-cn.com/problems/sorted-merge-lcci/](https://leetcode-cn.com/problems/sorted-merge-lcci/)
- MySQL 隔离级别
- MySQL 锁
- MySQL 存储结构（b+树）
- 索引 回表 是什么
- 消息队列，rabbitmq
- rabbitmq 如何保证可靠性（生产者可靠性、消费者可靠性、存储可靠性）
- rabbitmq 几种模式
- es 索引的过程
- 线上是如何分表分库的，用什么做分表分库的策略，跨表查询
- MySQL 如何同步到 es
- 线上 Redis 用的是什么模式
- 缓存热 key 怎么办

# 二面

- 介绍项目
- defer 、go 继承，手写快排
- 登录流程，JWT、session、cookie

# 三面

- 缓存一致性
- Redis key 统计
- Redis 单线程，io 多路复用
- 算法题 [https://www.nowcoder.com/practice/35119064d0224c35ab1ab612bffee8df](https://www.notion.so/35119064d0224c35ab1ab612bffee8df)
- Redis slowlog 原理

# 四面（面委）

- 项目为主
- tcp quick_ack 、 nodelay ，socket 编程
- 职业规划
- 为什么换工作

# 五面（GM）

- 项目
- go 协程机制
## Go相关

* map、channel、slice的底层实现，
* sync.map、sync.pool、sync.Once的原理
* GC的过程、写屏障的含义及作用
* GMP模型，触发Goroutine切换的原因有哪些？for死循环会怎么样？全局goroutine里面存储什么？
* interface的底层实现，怎么判空？
* reflect的使用
* 逃逸分析
* context的使用
* go 性能问题的定位过程（pprof的使用）
* 协程池的使用

## Mysql相关

* 索引的分类
* 为什么选择B+树实现索引？一般深度为多少？b+树和红黑树的区别?
* 聚簇索引和非聚簇索引的区别
* 创建索引后，查询读取I/O的次数
* 索引的最左前缀原则
* mysql数据的索引优化以及失效
* 从学生表中查询每个班的分数的前3名
* mysql的隔离级别？处理什么问题的（脏读、幻读、不可重复读）
* mysql的主从复制过程？
* mysql的大表优化方式


## Redis相关
* redis的数据类型以及日常的应用
* redis的发布/订阅的原理
* zset的底层实现
* 数据缓存过期策略
* redis的部署模式
* redis为什么速度比较快
* reids的大key、热key的处理
* 如何实现分布式锁的
* 持久化策略及其对比
* 缓存雪崩、缓存击穿、缓存穿透

## Etcd相关

* etcd是什么？有什么优势
* raft选主逻辑
* 日志复制
* 脑裂问题
* etcd的watch机制
* etcd如何实现配置下发和服务发现
* etcd对于偶数机器的集群的选主处理
* 选主实现逻辑

## Prometheus相关

* 简介
* 数据存储原理
* 数据类型

## Grpc相关

* 相较于restful的优势
* 数据交互方式
* 限流（通过流模式传输时，发送方数据量过大，会发生什么？）
* protobuf和json的对比
* grpc负载均衡的实现

## Linux相关

* awk
* poll、epoll、select
* I/O模型

## 网络协议相关

* http2的优势
* https的建连过程（7次握手）
* 对称加密和非对称加密
* icmp协议的原理
* tcp三次握手、四次挥手
* tcp 拥塞策略
* tcp的time_wait状态和colse_wait状态
* 如何解决tcp的粘包问题
* quic协议是什么
* 如何理解网络模型
* http的状态码含义

## 智力题

* 25匹马，每次只能比赛5组，最快几次找到前3名

* 宝石问题（3个盒子，其中2个宝石，一个石头；先随机选取一个，然后剔除剩余两个中的宝石；第三次选择，选择哪个为宝石的概率大？）


## 系统设计

* RPC的设计

* 架构设计分单系统，每秒3000订单有效期15分钟，50W司机进行抢单操作，如果一直没有抢单，则订单失效

* 字符串hash算法的实现
* 敏感词过滤
* 设计一个高可用的稳定的并发模型处理HTTP请求

## 其他
* 一致性hash算法
* 微服务概述
* 什么是死锁，如何避免
* 限流策略

## 算法
* 如何原地交换两个数

* 岛屿问题

* 数组中重复的数据  

* 1到n乱序排列的数据，少了其中一个，找出这个数

* 二叉树的右视图

* LRU缓存机制  （考虑并发访问）

* 高并发的生产者消费者模式

* 通过中序遍历序列和先序序列恢复二叉树

* 爬楼梯问题

* 单链表逆序

* 单向链表排序

* string1 = 1234dsafaserewr，string2 = 23aefasdfwer，求string3 = string1 + string2

* 二叉树节点的公共祖先

* 二叉树的最大深度

* 二叉树的中序遍历和层次遍历

* 寻找两个升序数组的第K大值

* 最长回文子串长度

* 最短回文串

* 合并两个有序链表  

* 全排列

* 接雨水

* 盛最多水的容器  

* Pow(x, n)  

* 合并两个有序的单链表

* 数组中值出现了一次的数字

* 判断二叉树是否为平衡二叉树

* 找到字符串的最长无重复字符子串

## 海量数据处理问题（面试官很喜欢问）
* hash
* 字典树
* bitmap
* 布隆过滤器
* MapReduce
* 桶

  

  


### 一面

二维数组二分查找，简历项目与挑战，redis 相关，如何实现timer

### 二面

循环队列，亮点与挑战Attribution-ShareAlike 4.0 International

=======================================================================

Creative Commons Corporation ("Creative Commons") is not a law firm and
does not provide legal services or legal advice. Distribution of
Creative Commons public licenses does not create a lawyer-client or
other relationship. Creative Commons makes its licenses and related
information available on an "as-is" basis. Creative Commons gives no
warranties regarding its licenses, any material licensed under their
terms and conditions, or any related information. Creative Commons
disclaims all liability for damages resulting from their use to the
fullest extent possible.

Using Creative Commons Public Licenses

Creative Commons public licenses provide a standard set of terms and
conditions that creators and other rights holders may use to share
original works of authorship and other material subject to copyright
and certain other rights specified in the public license below. The
following considerations are for informational purposes only, are not
exhaustive, and do not form part of our licenses.

     Considerations for licensors: Our public licenses are
     intended for use by those authorized to give the public
     permission to use material in ways otherwise restricted by
     copyright and certain other rights. Our licenses are
     irrevocable. Licensors should read and understand the terms
     and conditions of the license they choose before applying it.
     Licensors should also secure all rights necessary before
     applying our licenses so that the public can reuse the
     material as expected. Licensors should clearly mark any
     material not subject to the license. This includes other CC-
     licensed material, or material used under an exception or
     limitation to copyright. More considerations for licensors:
    wiki.creativecommons.org/Considerations_for_licensors
    
     Considerations for the public: By using one of our public
     licenses, a licensor grants the public permission to use the
     licensed material under specified terms and conditions. If
     the licensor's permission is not necessary for any reason--for
     example, because of any applicable exception or limitation to
     copyright--then that use is not regulated by the license. Our
     licenses grant only permissions under copyright and certain
     other rights that a licensor has authority to grant. Use of
     the licensed material may still be restricted for other
     reasons, including because others have copyright or other
     rights in the material. A licensor may make special requests,
     such as asking that all changes be marked or described.
     Although not required by our licenses, you are encouraged to
     respect those requests where reasonable. More_considerations
     for the public:
    wiki.creativecommons.org/Considerations_for_licensees

=======================================================================

Creative Commons Attribution-ShareAlike 4.0 International Public
License

By exercising the Licensed Rights (defined below), You accept and agree
to be bound by the terms and conditions of this Creative Commons
Attribution-ShareAlike 4.0 International Public License ("Public
License"). To the extent this Public License may be interpreted as a
contract, You are granted the Licensed Rights in consideration of Your
acceptance of these terms and conditions, and the Licensor grants You
such rights in consideration of benefits the Licensor receives from
making the Licensed Material available under these terms and
conditions.


Section 1 -- Definitions.

  a. Adapted Material means material subject to Copyright and Similar
     Rights that is derived from or based upon the Licensed Material
     and in which the Licensed Material is translated, altered,
     arranged, transformed, or otherwise modified in a manner requiring
     permission under the Copyright and Similar Rights held by the
     Licensor. For purposes of this Public License, where the Licensed
     Material is a musical work, performance, or sound recording,
     Adapted Material is always produced where the Licensed Material is
     synched in timed relation with a moving image.

  b. Adapter's License means the license You apply to Your Copyright
     and Similar Rights in Your contributions to Adapted Material in
     accordance with the terms and conditions of this Public License.

  c. BY-SA Compatible License means a license listed at
     creativecommons.org/compatiblelicenses, approved by Creative
     Commons as essentially the equivalent of this Public License.

  d. Copyright and Similar Rights means copyright and/or similar rights
     closely related to copyright including, without limitation,
     performance, broadcast, sound recording, and Sui Generis Database
     Rights, without regard to how the rights are labeled or
     categorized. For purposes of this Public License, the rights
     specified in Section 2(b)(1)-(2) are not Copyright and Similar
     Rights.

  e. Effective Technological Measures means those measures that, in the
     absence of proper authority, may not be circumvented under laws
     fulfilling obligations under Article 11 of the WIPO Copyright
     Treaty adopted on December 20, 1996, and/or similar international
     agreements.

  f. Exceptions and Limitations means fair use, fair dealing, and/or
     any other exception or limitation to Copyright and Similar Rights
     that applies to Your use of the Licensed Material.

  g. License Elements means the license attributes listed in the name
     of a Creative Commons Public License. The License Elements of this
     Public License are Attribution and ShareAlike.

  h. Licensed Material means the artistic or literary work, database,
     or other material to which the Licensor applied this Public
     License.

  i. Licensed Rights means the rights granted to You subject to the
     terms and conditions of this Public License, which are limited to
     all Copyright and Similar Rights that apply to Your use of the
     Licensed Material and that the Licensor has authority to license.

  j. Licensor means the individual(s) or entity(ies) granting rights
     under this Public License.

  k. Share means to provide material to the public by any means or
     process that requires permission under the Licensed Rights, such
     as reproduction, public display, public performance, distribution,
     dissemination, communication, or importation, and to make material
     available to the public including in ways that members of the
     public may access the material from a place and at a time
     individually chosen by them.

  l. Sui Generis Database Rights means rights other than copyright
     resulting from Directive 96/9/EC of the European Parliament and of
     the Council of 11 March 1996 on the legal protection of databases,
     as amended and/or succeeded, as well as other essentially
     equivalent rights anywhere in the world.

  m. You means the individual or entity exercising the Licensed Rights
     under this Public License. Your has a corresponding meaning.


Section 2 -- Scope.

  a. License grant.

       1. Subject to the terms and conditions of this Public License,
          the Licensor hereby grants You a worldwide, royalty-free,
          non-sublicensable, non-exclusive, irrevocable license to
          exercise the Licensed Rights in the Licensed Material to:
    
            a. reproduce and Share the Licensed Material, in whole or
               in part; and
    
            b. produce, reproduce, and Share Adapted Material.
    
       2. Exceptions and Limitations. For the avoidance of doubt, where
          Exceptions and Limitations apply to Your use, this Public
          License does not apply, and You do not need to comply with
          its terms and conditions.
    
       3. Term. The term of this Public License is specified in Section
          6(a).
    
       4. Media and formats; technical modifications allowed. The
          Licensor authorizes You to exercise the Licensed Rights in
          all media and formats whether now known or hereafter created,
          and to make technical modifications necessary to do so. The
          Licensor waives and/or agrees not to assert any right or
          authority to forbid You from making technical modifications
          necessary to exercise the Licensed Rights, including
          technical modifications necessary to circumvent Effective
          Technological Measures. For purposes of this Public License,
          simply making modifications authorized by this Section 2(a)
          (4) never produces Adapted Material.
    
       5. Downstream recipients.
    
            a. Offer from the Licensor -- Licensed Material. Every
               recipient of the Licensed Material automatically
               receives an offer from the Licensor to exercise the
               Licensed Rights under the terms and conditions of this
               Public License.
    
            b. Additional offer from the Licensor -- Adapted Material.
               Every recipient of Adapted Material from You
               automatically receives an offer from the Licensor to
               exercise the Licensed Rights in the Adapted Material
               under the conditions of the Adapter's License You apply.
    
            c. No downstream restrictions. You may not offer or impose
               any additional or different terms or conditions on, or
               apply any Effective Technological Measures to, the
               Licensed Material if doing so restricts exercise of the
               Licensed Rights by any recipient of the Licensed
               Material.
    
       6. No endorsement. Nothing in this Public License constitutes or
          may be construed as permission to assert or imply that You
          are, or that Your use of the Licensed Material is, connected
          with, or sponsored, endorsed, or granted official status by,
          the Licensor or others designated to receive attribution as
          provided in Section 3(a)(1)(A)(i).

  b. Other rights.

       1. Moral rights, such as the right of integrity, are not
          licensed under this Public License, nor are publicity,
          privacy, and/or other similar personality rights; however, to
          the extent possible, the Licensor waives and/or agrees not to
          assert any such rights held by the Licensor to the limited
          extent necessary to allow You to exercise the Licensed
          Rights, but not otherwise.
    
       2. Patent and trademark rights are not licensed under this
          Public License.
    
       3. To the extent possible, the Licensor waives any right to
          collect royalties from You for the exercise of the Licensed
          Rights, whether directly or through a collecting society
          under any voluntary or waivable statutory or compulsory
          licensing scheme. In all other cases the Licensor expressly
          reserves any right to collect such royalties.


Section 3 -- License Conditions.

Your exercise of the Licensed Rights is expressly made subject to the
following conditions.

  a. Attribution.

       1. If You Share the Licensed Material (including in modified
          form), You must:
    
            a. retain the following if it is supplied by the Licensor
               with the Licensed Material:
    
                 i. identification of the creator(s) of the Licensed
                    Material and any others designated to receive
                    attribution, in any reasonable manner requested by
                    the Licensor (including by pseudonym if
                    designated);
    
                ii. a copyright notice;
    
               iii. a notice that refers to this Public License;
    
                iv. a notice that refers to the disclaimer of
                    warranties;
    
                 v. a URI or hyperlink to the Licensed Material to the
                    extent reasonably practicable;
    
            b. indicate if You modified the Licensed Material and
               retain an indication of any previous modifications; and
    
            c. indicate the Licensed Material is licensed under this
               Public License, and include the text of, or the URI or
               hyperlink to, this Public License.
    
       2. You may satisfy the conditions in Section 3(a)(1) in any
          reasonable manner based on the medium, means, and context in
          which You Share the Licensed Material. For example, it may be
          reasonable to satisfy the conditions by providing a URI or
          hyperlink to a resource that includes the required
          information.
    
       3. If requested by the Licensor, You must remove any of the
          information required by Section 3(a)(1)(A) to the extent
          reasonably practicable.

  b. ShareAlike.

     In addition to the conditions in Section 3(a), if You Share
     Adapted Material You produce, the following conditions also apply.
    
       1. The Adapter's License You apply must be a Creative Commons
          license with the same License Elements, this version or
          later, or a BY-SA Compatible License.
    
       2. You must include the text of, or the URI or hyperlink to, the
          Adapter's License You apply. You may satisfy this condition
          in any reasonable manner based on the medium, means, and
          context in which You Share Adapted Material.
    
       3. You may not offer or impose any additional or different terms
          or conditions on, or apply any Effective Technological
          Measures to, Adapted Material that restrict exercise of the
          rights granted under the Adapter's License You apply.


Section 4 -- Sui Generis Database Rights.

Where the Licensed Rights include Sui Generis Database Rights that
apply to Your use of the Licensed Material:

  a. for the avoidance of doubt, Section 2(a)(1) grants You the right
     to extract, reuse, reproduce, and Share all or a substantial
     portion of the contents of the database;

  b. if You include all or a substantial portion of the database
     contents in a database in which You have Sui Generis Database
     Rights, then the database in which You have Sui Generis Database
     Rights (but not its individual contents) is Adapted Material,

     including for purposes of Section 3(b); and
  c. You must comply with the conditions in Section 3(a) if You Share
     all or a substantial portion of the contents of the database.

For the avoidance of doubt, this Section 4 supplements and does not
replace Your obligations under this Public License where the Licensed
Rights include other Copyright and Similar Rights.


Section 5 -- Disclaimer of Warranties and Limitation of Liability.

  a. UNLESS OTHERWISE SEPARATELY UNDERTAKEN BY THE LICENSOR, TO THE
     EXTENT POSSIBLE, THE LICENSOR OFFERS THE LICENSED MATERIAL AS-IS
     AND AS-AVAILABLE, AND MAKES NO REPRESENTATIONS OR WARRANTIES OF
     ANY KIND CONCERNING THE LICENSED MATERIAL, WHETHER EXPRESS,
     IMPLIED, STATUTORY, OR OTHER. THIS INCLUDES, WITHOUT LIMITATION,
     WARRANTIES OF TITLE, MERCHANTABILITY, FITNESS FOR A PARTICULAR
     PURPOSE, NON-INFRINGEMENT, ABSENCE OF LATENT OR OTHER DEFECTS,
     ACCURACY, OR THE PRESENCE OR ABSENCE OF ERRORS, WHETHER OR NOT
     KNOWN OR DISCOVERABLE. WHERE DISCLAIMERS OF WARRANTIES ARE NOT
     ALLOWED IN FULL OR IN PART, THIS DISCLAIMER MAY NOT APPLY TO YOU.

  b. TO THE EXTENT POSSIBLE, IN NO EVENT WILL THE LICENSOR BE LIABLE
     TO YOU ON ANY LEGAL THEORY (INCLUDING, WITHOUT LIMITATION,
     NEGLIGENCE) OR OTHERWISE FOR ANY DIRECT, SPECIAL, INDIRECT,
     INCIDENTAL, CONSEQUENTIAL, PUNITIVE, EXEMPLARY, OR OTHER LOSSES,
     COSTS, EXPENSES, OR DAMAGES ARISING OUT OF THIS PUBLIC LICENSE OR
     USE OF THE LICENSED MATERIAL, EVEN IF THE LICENSOR HAS BEEN
     ADVISED OF THE POSSIBILITY OF SUCH LOSSES, COSTS, EXPENSES, OR
     DAMAGES. WHERE A LIMITATION OF LIABILITY IS NOT ALLOWED IN FULL OR
     IN PART, THIS LIMITATION MAY NOT APPLY TO YOU.

  c. The disclaimer of warranties and limitation of liability provided
     above shall be interpreted in a manner that, to the extent
     possible, most closely approximates an absolute disclaimer and
     waiver of all liability.


Section 6 -- Term and Termination.

  a. This Public License applies for the term of the Copyright and
     Similar Rights licensed here. However, if You fail to comply with
     this Public License, then Your rights under this Public License
     terminate automatically.

  b. Where Your right to use the Licensed Material has terminated under
     Section 6(a), it reinstates:

       1. automatically as of the date the violation is cured, provided
          it is cured within 30 days of Your discovery of the
          violation; or
    
       2. upon express reinstatement by the Licensor.
    
     For the avoidance of doubt, this Section 6(b) does not affect any
     right the Licensor may have to seek remedies for Your violations
     of this Public License.

  c. For the avoidance of doubt, the Licensor may also offer the
     Licensed Material under separate terms or conditions or stop
     distributing the Licensed Material at any time; however, doing so
     will not terminate this Public License.

  d. Sections 1, 5, 6, 7, and 8 survive termination of this Public
     License.


Section 7 -- Other Terms and Conditions.

  a. The Licensor shall not be bound by any additional or different
     terms or conditions communicated by You unless expressly agreed.

  b. Any arrangements, understandings, or agreements regarding the
     Licensed Material not stated herein are separate from and
     independent of the terms and conditions of this Public License.


Section 8 -- Interpretation.

  a. For the avoidance of doubt, this Public License does not, and
     shall not be interpreted to, reduce, limit, restrict, or impose
     conditions on any use of the Licensed Material that could lawfully
     be made without permission under this Public License.

  b. To the extent possible, if any provision of this Public License is
     deemed unenforceable, it shall be automatically reformed to the
     minimum extent necessary to make it enforceable. If the provision
     cannot be reformed, it shall be severed from this Public License
     without affecting the enforceability of the remaining terms and
     conditions.

  c. No term or condition of this Public License will be waived and no
     failure to comply consented to unless expressly agreed to by the
     Licensor.

  d. Nothing in this Public License constitutes or may be interpreted
     as a limitation upon, or waiver of, any privileges and immunities
     that apply to the Licensor or You, including from the legal
     processes of any jurisdiction or authority.


=======================================================================

Creative Commons is not a party to its public
licenses. Notwithstanding, Creative Commons may elect to apply one of
its public licenses to material it publishes and in those instances
will be considered the “Licensor.” The text of the Creative Commons
public licenses is dedicated to the public domain under the CC0 Public
Domain Dedication. Except for the limited purpose of indicating that
material is shared under a Creative Commons public license or as
otherwise permitted by the Creative Commons policies published at
creativecommons.org/policies, Creative Commons does not authorize the
use of the trademark "Creative Commons" or any other trademark or logo
of Creative Commons without its prior written consent including,
without limitation, in connection with any unauthorized modifications
to any of its public licenses or any other arrangements,
understandings, or agreements concerning use of licensed material. For
the avoidance of doubt, this paragraph does not form part of the
public licenses.

Creative Commons may be contacted at creativecommons.org.## 阿里算法笔试
1. 给出人数，任意的人数可以组成一个队伍，只要队长不同就算是不同的队伍一共有几种：
    比如： 共有2个人 ( a 和 b ) 那么可以：
   i.  a 独自一人
   ii.  b 独自一人
   iii.  ab 两人，a为队长
   iv.  ab 两人，b为队长
    所以一共有4种组合，写一个函数实现这样的效果
  
2. 在一个迷宫中，你可以上下左右个走一步这要消耗一个单位的时间，或者飞到对称点，这也要消耗一个单位的时间，s为开始，e为结束，0 为可以行走的路，#为路障不可以行走，如：
        S # 0
        # E 0
        0 0 0

 我们可以从S对称飞到右下角，然后往上再往左，一共消耗 3 个时间
写一个函数求到终点消耗时间最少的走法
## 面试小技巧

### 一. 面试前准备小技巧

#### 基础知识必备
* 数据结构与算法
* Go 基础
* Go 常用结构原理

#### 中间件相关

* 缓存(Redis为主)
* 消息队列(Kafka、RabbitMQ、RocketMQ)
* 数据库(MySQL)
* 搜索(ES)

#### 分布式相关

* 分布式理论(CAP、BASE)
* 分布式锁
* 分布式事务
* 分布式幂等框架&源码相关：Spring、MyBatis、SpringBoot、SpringCloud、RPC(如：Dubbo)、Netty等常⽤框架

#### 架构&容器相关

* 微服务架构(架构演进过程)
* 微服务拆分原则
* 容器化（了解Docker & Kubernetes）

#### 项⽬经验

* 项⽬整体架构(能画出来)
* 项⽬上下游关系(能将明⽩)
* 项⽬实现细节
* 项⽬主要亮点

#### 技术之外

你有什么要问我的吗？、离职原因？、职业规划？项⽬管理？⾃身优缺点？挫败感的事⼉？⾃身哪些需要提⾼的？绩效怎么样？突出贡献？

这些技能要想深⼊不是短时间内突击就能完成的，需要⻓期做项⽬加上平时的有⽬的性的学习和积累。技术栈⽐较多，我个⼈建议在⾯试前提前准备阶段，通过思维导图的⽅式梳理，对抗遗忘曲线，推荐使⽤ProcessOn⼯具。

### 二. ⾯试时⼩技巧

上⾯尽管罗列了很多技术栈，但是要尽量做到⼏个技术栈能更擅⻓⼀些，⽐如对 调优、并发、分布式等技术都有深⼊实践和研究，或者对某些框架源码深⼊研究，并做过⼆次开发之类的，都是有加分的。

因为⼤家不可能都是样样精通的，也最好别都样样稀松，所以关键夯实住⾃⼰擅⻓的，然后其他技术栈查缺补漏。

同时，在⾯试中开始⾃我介绍后，建议你要有意识的说出你擅⻓的领域、技术栈等。其⽬的也很简单，就是将⾯试官的思路往⾃⼰擅⻓的⽅向引导。不然呢，当⾃我介绍结束之后，⾯试官基本就按照他的套路出牌了，或许你此时会有点被动的~

但是，有的⾯试官也不吃这⼀套，那你就准备应战就好了。某些技术你简历写了，⾯试官正好问到了，但是其实你并不是很擅⻓，或者时间久没有了解的很深⼊，如果追着问，就有点尴尬了？

⼀般⾯试官都是结合你简历问的，很少⾯试官是只问他会的东东，如果真遇到这样的，也是有点尴尬，不⾯也罢！

### 三. 小结&问题答疑

最后也提醒各位要换⼯作的⼩伙伴，⼤家加油！

1. 社招算法：算法虽然在社招里面占据的比重不大（大概10%），但作为基础，也是要准备的。我这次也不是很充分，算法还在平时的积累和练习。
2. 思维导图：平时尽量画⼀画思维导图（processon），对抗技术知识的遗忘曲线。
3. 技术应⽤：这个是重点，将学到的技术应⽤到项⽬⾥，使你的项⽬看上去更有亮点，更有聊的价值。
4. 临场应变：有些题⽬会发散，要提⾼临场随机应变的能⼒
5. 技术积累：基础和经验相结合

举个例⼦：有⼀家 C 轮公司的⾯试官⽼哥，很直接的说我仅出两道题，剩下你来问我：

题⽬1: 输⼊⼀个⽹址⽐如 jd.com 到底发⽣了什么？请你讲的越详细越好
题⽬2: 后端服务如何做性能优化的？讲的越详细越好。

惊不惊喜意不意外啊。。。

第⼀个题⽬可以考察：⽹络、CDN、DNS解析、TCP 三次握⼿/四次挥⼿、Http协议。
第⼆个题⽬可以考察：DB、SQL(索引)、缓存、Go server等性能优化等等？

说是两个问题可以扩展出很多个问题，所以要回答的全⾯且到位，也不是那么容易的对吧。当你提到某个技术优化的时候他会详细追问如何做的，这哪是两个问题啊。这家公司也是⾯过了的，也具备⼀定的发展潜⼒，个⼈也是⾮常看好的。## golang new 和 make的区别
- make 返回的是值
- new 返回的是指针
- make 只能用于内置类型 这个是没有问题的
- 这要看对象所处的作用域，如果逃逸分析确定对象不会跑出方法里，是可以分配在栈上的
- 问：那map，slice 和 channel 可以用new 吗？
   不可以，有内置初始化操作
   ![make参数类型.png](https://upload-images.jianshu.io/upload_images/331298-01759889aa9ed5a1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- silce、map、channel等类型属于引用类型，引用类型初始化为nil，nil是不能直接赋值的

## golang 的 map 随机输出问题
- 今天看群里一个同学分享的文章说map实现的时候回产生随机的key
 解决这个问题的方法是先便利一遍取出所有的key，放到slice里面然后便利这个slice
  我觉得这用个是一个比较笨的方法，不知道大家有什么更好的解决方案
- lru听说过么[吃瓜]
 以来key输入次序，还是什么次序
- 但是如果这个key本身就是一个复杂的类型（结构体）这样就很复杂
- 用指针保存对象，用两个数据结构保存该指针，这样既能实现顺序遍历，也能实现快速插入
- 如果输入次序很简单，准备一个双链表+map就可以
- 这样子不是存储成本更高了
目的是为了key 顺序
- 空间换时间
说白了就是额外开空间保持key的顺序性[吃瓜]
- **那讨论一下go map 的底层实现吧**
- 散列表，压缩链表，溢出区
- go用的是散列表，为了防止程序员选手混淆map是否有序，故意在range的时候加入了随机，其实我觉得除了老版的C++程序员可能会混淆，也没啥人会误会map的用意
- 我的回答：go map 从底层结构看的话，其实在进行rehash 后才会造成顺序问题，是设计者为了不让大家依赖map 的顺序性，刻意每次输出都做随机输出，保证key 有序和上面说的方案一样，按顺序将key存取来，按key 取值
但是也是网上看的通用答案，没看过底层实现
- 为什么是rehash之后呢？可以说说吗
- go 的map hash冲突之后，链地址是物理上的链吗？
- map的随机是为了避免程序员习惯性依赖，把map当数组用
- 对，像PHP中array 和 map 都作为array来操作
- 我个人看法，如果学过离散数学，一对一和一对多的问题，知道map是键值对的集合，本质上就是一种1：1的数据结构，那么如何才能实现一对一的结构呢？第一种方法，用一个List保存所有元素，然后遍历这个数组，找到对应的key，拿到跟key绑定的value，可以想象，List也能实现一对一，但是查找很慢。在老版的C++中，他的hashmap用了红黑树来实现，而且支持顺序迭代，查找时间复杂度logN，之后我们可以看java，java的hashmap用了散列表来实现
假设我们的散列表的长度远远大于数据的范围，且元素单一无重复
如散列表长度1000000000000000000000000000000000
但是里面的元素只有1 2 3
只要使用hash算法 num % 100000000000000000000000000
-go 的map hash冲突之后，链地址是物理上的链吗？那是不是每次都要分配内存？
物理地址上的链没理解，应该是链表吧，不需要重新分配内存吧，把冲突数据的指针差到后面就行了
rehash的话需要重新开辟一块新的地址，用来存储扩容后的数据，一般是渐进扩容的方式
- go的是压缩链表，貌似是8个键值对压缩在一起，所以分配上也有不同之处，那就是假如8个未满，可能就不需要分配内存
物理链我也没懂，如果是面试官问的，我就会回答是指向下一块value的指针
噢，是连续的，我看到德莱文大佬的文章里又写了
![map底层.png](https://upload-images.jianshu.io/upload_images/331298-5c4036f6cf2612f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在bucket中迭代寻找值得时候，用的是指针偏移，这个应该可以说明键值对连续分配
![bucket迭代寻值.png](https://upload-images.jianshu.io/upload_images/331298-bc5ba54a2a502c1a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 设计题：短网址设计
- 还有个问题 http的长地址 对应的是http的短地址 https也对应的是https的短地址吗？
- 短地址和长地址是一对一关系，主要有两种方案，hash计算，和关系存储
-redis incr
然后 hash 这个
你用 MongoDB 的 _id 做短地址 都行
设置个 ttl 索引
有效期 都有了
讨论那么多 1个mongo搞定

## 题外话：
#### 位运算交换两数快还是使用中间变量？
1. 位运算走硬件
2. 现代编译器对两数交换做了优化

**golang benchmark如下图所示：**
![位运算交换.png](https://upload-images.jianshu.io/upload_images/331298-2e5907318fcaf8f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![中间变量交换.png](https://upload-images.jianshu.io/upload_images/331298-fe6ad94fad46f9b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 1.讲讲进程、线程，以及Go  goroutine
- 进程和线程的OS级别的 协程是用户态的
线程是具体执行者 进程负责为线程分配执行需要的资源
起到资源隔离作用
- 线程是调度单元
- 别的语言也有协程 比如yied
- 可以再说深一些，比如进程和线程哪些资源是共用的，哪些是独占的
- 先从进程和线程说起吧，应该都知道，进程是资源的封装单位，线程是操作系统的调度单位
- 一个程序至少有一个进程,一个进程至少有一个线程
- os 如何管理进程的，pcb 里大概存了些啥
- 进程调度也是维护可用进程队列的
- 我看网上资料将go 的goroutine 其实是用户级线程，相当于是用户级实现的线程库，对于cpu 来讲是不可见的，那用户级线程对cpu不可见的话，是怎么自己完成切换和调度的
- 为什么协程 是用户态?
用户态和内核态的区别和作用分别是什么？
- 因为携程由用户进程调度
在用户空间运行
- 调度的时候会进行指令切换。用户态切换到内核态，先把现在的进程信息保存起来，然后切换指令，再把将要执行的信息加载到内存，然后CPU再执行
- 这个得说明系统调用是干嘛的
用户态通过系统调用陷入内核态
喜兄弟说得对，只有内核才能调用系统硬件
- 如果我监听 键盘输入。
- 用户通过系统调用进入内核进行操作
- 内核空间 都是中断陷入进去的
- 这一操作。状态怎么转换的？
- 触发中断
硬中断
- **有没有调用system.call就是用户和内核分界线**
- 通过信号发生中断，根据一个编号啥的，进行指定的系统调用
- 什么叫系统调用
- 系统调用是用户操作内核的函数
- 个人理解，就拿线程和进程来说吧，两者关心的其实只有mmap，而mamp的实际指向的内存区域，只有陷入内核态才能发生改变，这里就限制了，无论线程和进程，如果想要进入实际内存，就只能通过内核
- 「 江边一只鸡: 个人理解，就拿线程和进程来说吧，两者关心的其实只有mmap，而mamp的实际指向的内存区域，只有陷入内核态才能发生改变，这里就限制了，无论线程和进程，如果想要进入实际内存，就只能通过内核 」
这块儿之间看过清华一教授的操作系统公开课里有讲，为了限制不同程序之间的访问能力，防止获取别的程序的内存数据，或者获取外围设备的数据，为了起到保护作用
目前问题点有以下：
讲讲进程、线程，以及Go  goroutine
进程和线程哪些资源是共用的，哪些是独占的
os 如何管理进程的，pcb 里大概存了些啥
用户态和内核态的区别和作用分别是什么？
-  就拿linux来说，线程和进程本质上都是一个个task，不同之处就是线程之间共享了task之间的堆和全局变量等资源，轻量级线程就是如此，至于goroutinue，这就是线程资源进一步复用的产物了
- **做goroutine池是否合理**
- 特殊场景下，限制数量还是需要的
协成多了cpu在调度上花费很多，目前正在做一个长连接接入服务，一条连接有两个协成分别进行读写，没请求的话单机能抗400w连接，但是实际场景是一条连接维持20s左右，一条连接平均发1次请求，结果一台机子只能抗住30w连接，qps也才1w多，pprof跑了下，调度占了快一半了。我准备换成这个框架看下效果
- **你大量创建的时候。他还来不及复用。 只能创建g 和sudog**
然后你创建的g 变成空闲的时候 是不会被回收的
但是 调度的很多地方都会遍历全局。
- go我的理解就是大量的函数调用，但是这个goroutinue却是中心单线程调度的，这就是goroutine的瓶颈所在
- 比如你一秒创建10万个。肯定来不及复用。 都是创建新的goroutine.
- 那这些大量创建的GR 什么时候会被回收呢
- 然后 可能这10万个 就一直在全局的 复用池了。
- 意思是，假设，我有500个链接，读写各一个 gr， 总共 100个。 但是底层其实没有 100个，会复用？
- 不会被回收。
- 这就吓人了 如果不限制数量 一个峰值打上去 后续都影响了
- 对。所以一般会控制数量
- 防止瞬间打高。
- 取决于你的机器资源。
- **先不说池了会不会快。快多少。**
这么玩 go官方知道吗
费了老大力气。让你直接go 你偏偏用c的线程池。何必啊
就算真快。我都不用。
还不一定快。。
- 只要防止服务瞬间请求过大。这属于服务限流问题了。
只是因为Go goroutine的特性。导致我们需要控制一下gr 的数量。
- 我是这样想的，go本身有goroutine的复用，合理控制goroutine的生命周期就好
**https://taohuawu.club/high-performance-implementation-of-goroutine-pool**
- **可以速度翻下 gin iris beego grpc 搞池没， 绝逼没搞！**

## 2.设计题：亿级视频帖子，评论表如何设计，要有弹幕功能
-  这种造航母设计题一定要先理解场景才好下手
弹幕有一点就是不管是否发送成功，只要给发送者显示出来就算成功，所以系统设计考虑这一点
1.用户订阅一个房间的主题，朝这个房间发弹幕就是推送消息，消息允许丢失，别的用户通过订阅这个主题获取其他用户的弹幕消息
2.弹幕存贮的问题，直播我不太会，说一下我理解的”时间线“，弹幕按照时间线存储
3.回看的时候，弹幕消息以流的方式取出来和视频一起播放
- 对的，弹幕需要一个消息中继的地方，收集弹幕消息定时批量推送给用户
恩恩，要么满足时间条件要么满足最大值
视频流和弹幕最好不要脱节
直播的时候也是，流量大了，通过CDN分发出去
- 弹幕设计思路：
推：用户发送弹幕消息通过长连接发给类似broker的角色。
存：broker将弹幕消息批量刷盘，并推送给用户
取：用户通过长连接接收，broker推送过来的弹幕消息。
并发：长连接接收推送消息并发量千万级可以，亿级加机器？我没做过。
惊群：为什么会出现惊群效应呢？
- **视频的话是要丢CDN的，所以压力也不在server**
- 亿级呢，长连接怎么做，然后需要几个broker，broker之间的同步
干脆不同步了
- 所以根本不需要做存储 那么核心问题就是怎么维持长链接了
对于维持无状态的长链接是比较简单的问题了 注意是无状态的
假如是一亿 按照用户ID进行128取模数 那意味还不到一百万
- 为什么要128
- 没啥讲究 就是为了模数对称 方便扩容
理论一个 生产主备就好了
客户端那边做自动断线重连功能
不是一个broker 是多个broker 来分担这一亿连接
- **实时的弹幕消息用长连接，那历史消息呢？**
**弹幕这块儿怎么存储，能按视频的播放进度显示当时发弹幕的**
- 也有点类似kafka的offset
不过这个offset一次跳一批，不是一条条跳
- @阿郎 按理来说用流的方式取弹幕消息不是更好
- 看视频app 的弹幕是根据进度条，相对时间记录弹幕内容
- 「 懒懒: 存文件吗？那新增的弹幕怎么写入文件 」
新增的时间线弹幕，例如回看的时候在开头插入
- 这个看设计了，要是弹幕多的话，谁知道保存没保存
- 「 阿郎: 回放新增的弹幕似乎很少看到保存的 」
不保存这种是直播场景，像视频播放这种，比如B站、爱奇艺、腾讯视频什么的，视频弹幕的场景
- **https://zhuanlan.zhihu.com/p/36024676**
看看别人怎么搞的。美图这个架构看着也不复杂
















### 一、http 和https ，http1.0、1.1、2.0的区别，https 加密过程
rpc  相较于传统api 调用的优点，如何设计实现一个rpc
- http和https相比性能差距有多大
- 理论上讲应该就多个加密解密的过程吧
- 这个过程就够了。。。太费 CPU 了
- ecc 会比 rsa 快吗，快多少？
- 时间是花在握手上还是加密解密上？
- 单台 Nginx HTTP 可以支撑几万连接，HTTPS 只能几千
再多 CPU 要爆
- 应该在加解密吧
握手http也有这个过程
- 再抛一个常问的，https是对称加密还是非对称加密
- 最初建立连接时，会进行两次非对称，一次对称，之后传输数据都是对称了
- 两次非对称 
1次是服务端加密
1次是客户端验签吧
- 1-1000倍，我不知道哪里看的数据
- 「 子杨: 「懒懒：
最初建立连接时，会进行两次非对称，一次对称，之后传输数据都是对称了」
两次非对称怎么解释 」
一次是ca 证书，数字签名验证，一次是传输对称加密
- 数字签名包含啥，以及验签流程是啥
- 数字证书里，有服务器公钥和数字签名，数字签名，是ca机构通过摘要算法，提取出公钥的摘要，然后用ca机构的私钥对摘要加密，生成数字签名
- tls1.3 和 tls1.2 啥区别
- 1.2三次握手，1.3一次
- 我不理解的就是，公钥干啥用的
- 公钥验签
私钥加密
- 告诉和你通信的另一方之后发消息用公钥加密后再发
这块儿就是客户端拿到服务端的公钥好，验证通过了，将对称加密用的密码串用这个公钥加密后告诉服务器
- 并不一定必须通过CA
- 公钥加密 私钥解密
- 前几天我还做了一个
- 私钥加签 公钥验签
- 但是怎么验证没搞懂
- 自己生成一对，进行加解密你就知道了 
- 我觉得不是这样，ECDH 算法并不需要传递对称加密的秘钥，因为这个算法保证了双方生成的秘钥一定相同，所以我觉得应该是加密 ECDH 算法所需要的参数
- 自签名证书 别人浏览器或者操作系统没有权威CA机构颁发的生疏，浏览器就默认拒绝加载 
@牛晓东 看看pki 和 CA原理 
- 我去搜搜这个ECDH
- 「 子杨: 「懒懒：
这块儿就是客户端拿到服务端的公钥好，验证通过了，将对称加密用的密码串用这个公钥加密后告诉服务器」
我觉得不是这样，ECDH 算法并不需要传递对称加密的秘钥，因为这个算法保证了双方生成的秘钥一定相同，所以我觉得应该是加密 ECDH 算法所需要的参数 」
对称加密的秘钥不是直接传的，而是在之前的握手过程中双方拿到了相关参数，然后双方各自生成的。 这样讲对吗？
- 是说那个密码串是个参数是吗？
[https://time.geekbang.org/column/article/644b842ad2661608bd05b071e9dc55b1/share?code=SPwAUY1BgfghtU7s6iiIoxVchKtu2XVGCy2kEIeJP%2F4%3D](https://time.geekbang.org/column/article/644b842ad2661608bd05b071e9dc55b1/share?code=SPwAUY1BgfghtU7s6iiIoxVchKtu2XVGCy2kEIeJP%2F4%3D)
- 随机生成器的吧 共享密码串 还有时效呢 我记得根据RSA参数随机生成的一个共享密码串 然后后续用这个密码串进行对称加密 
- 所以我始终没搞明白数字证书里面的公钥是干啥的，如果要用来加密的话加密哪个东西。
- 验证证书的签名
- 证书里的公钥 是用来验证网站身份的；那怎样证明这个证书是真的？  用Ca的公钥验证 证书签名
- 对称密钥是  客户端 和 服务端 分别通过三个随机数算出来的，且双方算出来的对称密钥是一样的
前两个随机数是明文传给对方的 ，第三个随机数的生成方式  会根据选择的密码套件  而略有不同
### 二、rpc  相较于传统api 调用的优点，如何设计实现一个rpc
- 感觉用grpc省事一些。
- 适合内部服务调用，
- 不然每次调整接口。上下游都要调整
多一个参数，少一个参数的。版本很难控制
- 为什么选择grpc，不用restful呢？这个问题面试官可以经常问￼
- 这让我想起了树哥的话，事件驱动和命令驱动
- grpc可以长连接
还可以双向通信
- restful 设定keepalive不行吗？
- 那个还是ws
http其实可以有状态
就是up成ws
- 感觉RPC 和 HTTP 放一起比较还不太合适。RPC可以看做一个概念，HTTP 可以看做一种通信协议。RPC 可以选择基于 HTTP，也可以选择自己定制。
嗯对，很大。restful 是不是也能叫一种远程调用。
- 我自己进行rpc的定义
- grpc怎么做限流呢？需要外部提供限流吗？
- gateway 就顺便做了
- grpc支持双端流通信，如果发送端发送的数据量很大，接收端处理比较慢，会出现什么状态呢？
- 积压， 和消息队列一样
- 消息队列会削峰的，不一样
- 生产者能力强消费者能力弱
- 此削峰非彼削峰
- 消息队列都是拉取模式。削峰了。rocket kafka
比如拉了1000个。没处理完 不会在拉
- 峰值削的是写入数据库的，其实是堆积在消息队列里的
是啊，生产者能力强，消费者能力弱
消费不完，生产者还在生产
- 那grpc呢，存哪？
- 母鸡🐔 只是我的猜测，会不会积压
只是积压在哪里的问题
- 我也母鸡 。我很排斥rpc。。。哈哈
- 我当时说的是grpc会有一个缓存区，缓存区满的话，会被直接拒掉￼
- 问这种题目的意义何在
这个底层也就是http2.0
也就是tcp
![http处理.jpeg](https://upload-images.jianshu.io/upload_images/331298-2ca8bff5153dc496.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 处理不过来就超时呗
- 考察grpc双端流的通信机制吧
- 这个是http的代码。并没做什么处理
至于grpc内部有没处理 就不了解了。比较排斥。不想看
- tcp处理不过来，堵塞，丢包，触发拥塞控制？
- 会触发超时
- 超时后，这次双端流交互就结束了？
- 不至于结束
grpc本身有没有控制处理我也没看
grpc是个框架
他里面会要么自己处理这种
- 这个参数也没个注释……
- 客户端结束 服务端没有
- 有重试
![grpc重试.jpeg](https://upload-images.jianshu.io/upload_images/331298-540847787fbe41d4.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)









### 接昨天题目，继续讨论，如何设计实现一个rpc
今天继续讨论rpc，@阿郎 这块儿有在做rpc，辛苦今天跟大家分享一下
- 晚上分享一下我对rpc的理解 有不恰当的地方 欢迎大家斧正 也期待群里面同学有更深的见解
应该这么描述 rpc 过程 你们觉得有什么组件参与在里面 ？
- 数据封装和传输
- 序列化和反序列化 
- 太具体了 感觉还是抽象不够
- rpc屏蔽了应用层以下的所有操作
- 我觉得rpc 只是为了通信 协议更方便~
然后就是客户端 服务端的协议通信~ 看如何实现
- 我从自己认知里面来说这个过程 是怎么看待一个rpc过程
rpc 实质是一个远端对象与另一个远端对象进行消息呼唤的过程
- 不分服务端和客户端的概念吗
- 从发起角度来看就是服务端和客户端的概念
- 协议，序列化，拦截器，负载均衡，反序列化
是不是还有安全
- 但是说起服务端和客户端 很容易就想起 网络传输
- 我觉得这个地方是个误区
- 同进程里面实际上还是可以进行rpc互换的
- 「 adslen: 是不是还有安全 」
安全是不是rpc的必选项
说个我们业务场景的例子
我们研发的业务很分散 人员之间负责的业务比较独立 但是在线上发布的时候 又希望是一个整体
- 没太懂，如果理解成一个远端对象和另一个远端对象进行交互的话，这两个对象应该都可以发起操作，但rpc实际只能由客户端主动发起操作吧？
- 对于这种业务场景 假如让你们来设计这个不同业务之前相互协调的rpc 过程 你们能想到怎么来设计？
- 双向是指传输，发起只能一端吧
- 双向是指通道
- 「阿郎：我们研发的业务很分散 人员之间负责的业务比较独立 但是在线上发布的时候 又希望是一个整体」
独立开发，一起上线的意思？
- 是的
因为我们部分业务是给客户提供一些的业务模块组合的 然后私有部署的
对于前面的问题 我抛出来的解题思路是 将rpc 过程抽象化 即 rpc过程不在关心具体是怎么实施 也不去考虑怎么来做消息传输 只去考虑rpc的约束
- rpc约束是啥意思
- 函数输出和输入？
- 「 奔跑: rpc约束是啥意思 」
就是你写的pb文件里面的rpc
这个约束意思指rpc调用的规范 即你只能什么请求、传什么参数、拿到什么结果
- 哦哦，通过PB文件进行约束，是不是就是pb文件即接口文档
- 先看定义，rpc：Remote Procedure Call 远程过程调用。
本地调用了一个函数，实际上具体的逻辑实在远程的机器上来执行的。

本地函数调用 - 传输 - 远程函数执行
（客户端） - x - （服务端）

这样的话至少需要把中间这个过程封装起来，把本地函数调用转换成网络请求，传递到服务端，再调用服务端的函数，然后把结果返回回去。
需要服务端启一个 server，然后这边客户端按对应的去调用。
需要实现这个客户端和这个服务端。

可能客户端和服务端的代码是异构的，这样就需要中间用一种通用的描述语言来描述字段，就是 IDL（Interface description language，接口描述语言）。
可以用 IDL 描述接口的传入、返回值，按需转换成客户端、服务端所使用的编程语言的原生表示。

客户端、服务端代码要手写比较麻烦，就可以在加上代码生成工具。

像微服务这种节点很多就再加上服务发现之类的机制。

- 这个过程就是误区了
![误区.jpeg](https://upload-images.jianshu.io/upload_images/331298-2b66eed22194a09b.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
其实rpc调用过程是不限制传输途径的
网络只是其中的一种
- 我给画一个通用型的rpc 过程哈
 ![rpc过程图](https://upload-images.jianshu.io/upload_images/331298-92acdeb7fbb5378e.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 在本机就完成了交互，而不会跨网交互
- 我觉得一个最简单的rpc 过程应当具备上面要素
- 其中transport 不应该去限制它到底是什么样方式的
- gokit就是这路子
- client 完成请求的封包以后 通过transport 的通道将数据传递给server 然后server响应 通过transport 回传回来
- "adslen: 「clip：
可以抽象一下，但是主要还是网络吧 ￼」
一提socket，你是不是第一反应网络传输￼"
是啊 ￼
- 在这个过程中 没有绝对的那个应用程序就是client 那个就是server 一个应用程序能在一个transport 的不同通道之间即充当client 又可以是server
将一个client 请求 server 看作是invoke，server 就是一个Register 就可以了
- 总有一个 call 还有一个被 call。生成代码的时候也有 client 代码和 server 代码。
不过两个服务的话可以都启动 server 代码，然后用对方的 client 代码互相调用。
我见过的是这样的 ￼
不确定有没直接就做在一起的
- 「 阿郎: 将一个client 请求 server 看作是invoke，server 就是一个Register 就可以了 」
这段话 用代码 是不是转换成 type Client interface { 
	Invoke(interface{}...) error
}
type Server interface {
	Register(interface{}...) error
}

- 上面其实抽象是有问题的 希望大家考虑一下问题在哪里
- "阿郎: 上面其实抽象是有问题的 希望大家考虑一下问题在哪里"
你具体是说哪一个有问题 ￼
- 主要问题出现在client
如果有扒过grpc源码的老哥 应该能体会问题
上面的client 没有描述一个rpc的idl 的约束信息
贴一小段grpc的代码
![grpc源码](https://upload-images.jianshu.io/upload_images/331298-a90bf5f460b435a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 这个是一个pb 的idl
这个是grpc
client 的抽象
![client 的抽象.png](https://upload-images.jianshu.io/upload_images/331298-7f0b4ff8eb717883.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这几个参数 就是rpc client的精髓
![精髓](https://upload-images.jianshu.io/upload_images/331298-85bb085d484b7c31.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
「 小龙: restful也可以实现，为什么选择rpc呢，毕竟restful更通用 」
我觉得在这里 可以回答 这位兄弟的疑惑了
上面的疑惑 应该是把rpc的实现与rpc过程给混谈了
- 「 阿郎: 「 小龙: restful也可以实现，为什么选择rpc呢，毕竟restful更通用 」
我觉得在这里 可以回答 这位兄弟的疑惑了 」
补充一句，restful的HTTP协议有很多空间浪费的地方，性能也不好
- 这么描述 不知道大家能否理解一个rpc 如果可以理解 可以转向怎么提升rpc qps的话题嚒￼
- 提升rpc性能的话，指的是业务手段，还是代码级别去提升呢
- 应该是说提升性能的方向吧
- 性能主要还是在业务的
大部分项目都还没到去扣代码细节的地步吧
- rpc qps 提升 我今天看到一图 挺好的 分享一下
![grpc流程图](https://upload-images.jianshu.io/upload_images/331298-e6d7b92aec5f17f8.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 我想到的也是限流，不过缓冲队列更好，虽然实现起来复杂一点
- 队列的好处是充分来利用了连接 限流是保障
- 你们业务用上了么
超时时间怎么算的
队列容量怎么考虑的
还有重试、
- grpc里面的队列没找到在哪里实现的￼
- 我负责的部分业务 对rpc 的qps 吞吐比较敏感 做rpc 过程 有比较深刻体会：小粒度锁、异步发送这两个手段才是最大性能的
提升点
- 是的，异步化对性能提升很大
为啥要加锁，什么场景
- 异步无锁+超时太难控制了 后面选取的是小粒度锁
连接池 在rpc过程中 不要去做扩容 固定就好 大不了丢一些
扯了挺多哈 关于rpc 不管怎么变化 还是上面图里面的那三个东西  希望对大家在涉猎rpc框架时 有所帮助








#### 1.Redis热key，有什么问题，要注意什么，如何解决
#### 2.Redis大key，有什么问题，要注意什么，如何解决
- 这个是 之前同事 PHP-3年的面试题. 这其中有redis 热key 的问题  和大key 的问题
https://github.com/xiaowei520/go_interview/blob/master/%E9%9D%A2%E7%BB%8F%E5%88%86%E4%BA%AB/PHP-3%E5%B9%B4%E7%A4%BE%E6%8B%9B-202004.md
大家今天可以讨论下 redis 热key 和大key 这两个问题。   什么场景下会引发什么问题，怎么解决。等
- redis 大key内存加载慢
删除的话，也需要一定时间。如果能分批删除的话可以脚本分批删除吧，比如大集合。。对吗
- 为什么会加载慢？
- 内存分页加载，不会慢吗？
- 我对一个key list 里面 不断地push 东西。 他会越来越慢吗？
- 追加的话就不会慢了
- **所以他这个大key 应该是  遍历才会出现一些问题哇！**
获取 获取这个key下面全量的元素。
- 整体加载。整体删除
- 我对一个特别大的key  list 删除。  会慢吗？
比如list 下面1000万元素
我直接删除这个key.  会慢吗
- set会慢
List不会慢吗

#### 引申讨论：微博点赞计数该怎么存
- 微博点赞计数该怎么存
- 你不就是微博的。
- [粉丝关系链，10亿数据，如何设计？](https://mp.weixin.qq.com/s/S1hU7oX7rum01g_Xwixunw)
- 去重很难
粉丝列表和关注列表很难
- 每个评论或微博存一个key？
- 去重，遍历就可以了
集合，分区，分类，建立索引
- [30WQPS的点赞计数业务，如何设计？](https://mp.weixin.qq.com/s/vKV0ENUdCmjgRvt8MV-C2Q)
- 这个就只是落点命中
- 28原则去考虑的话，80%的都是很少赞
**稀疏数组**
- 像点赞，不是时时的
- 嗯，感觉存数的这种，大体量的都会考虑内存利用
- 为什么像光点赞这种。 只要列出前100个点赞用户的头像就行了吧。
其他的用户的点赞 某一个微博文章，只要 累计总数 ++ 就行了。  然后用户自己维护自己的点赞记录。
- 后面的数字多少都不需要非常精确
不需要强一致了
- 点赞还有通知呢哈哈
- 点赞只涉及到 + - 、
点赞通知异步到队列。 通知是通知。
- **点赞数据持久化  强一致性，方案怎样才合理**
- 大规模强一致吗？
保证每个都要点上？
确定强一致？
- 面试确实被问过
- 也是，除了面试哪个脑残搞这个。。。
- 这种都只能最终强一致。
实时强一致性，自然会损失性能。
- 那我们围绕强一致讨论
一个是redis的inc加···
- 对啊，有舍有得，哪有这么完美的
性能没了，强一致屌用
- 分业务场景
CAP
- 但是一般互联网业务不会选择CP，都是倾向AP
- 银行业务就是强一执
互联网的话 最终一致
- 「 孙国栋: 性能没了，强一致屌用 」
老哥说的是，所以···哈哈，这种问题就面试问
确实尴尬，又要几十万百万人的大规模，还要保证强一致
redis吧，一秒写10w行吗，一百万人也要等十秒，系统hang住
- 怎么避免大key
- 怎么统计热点key￼
#### 全新思路解决热key问题！！！
- **热点key 就是并发大嘛。。直接防击穿的原理，幂等的访问就行了。100万个并发请求。 其实只请求一次。99.9999万，锁住共享数据了。**
- 最好上代码，你的freedom
- 不是缓存啊。 缓存我的理解是需要考虑过期时间的
singleflight 的原理是锁key
10万个并发去访问某个缓存key。 锁住，让一个并发去访问。 其他9.999999万 直接用那一个并发的数据就行了呗。
- 最后解决 和树哥说的差不多， 知道是热点key 。直接读取proxy 的内存就行了。
现在是如何定位 热点key. 这个才有意思。
- 我这边是不区分热点和非热点。直接中间件singleflight
- 检测到时热点key. 下次遇到热点key 的请求， 直接读内存的数据。 申请个内存 很简单啊
- 我觉得最好别存的内存里。 过期，二义性。不好处理。。。就锁key 即可
如果并发1个。那就1个去redis读。。如果并发10万。也还是1个去redis读
- 有点明白了，server中间件处理了对吧
艹树哥这个好
不需要做热不热key的计算了
- git发一下
- https://github.com/8treenet/freedom
大家记得点个赞





### 今日主题：
1. 跳槽的平跳，仰跳和俯跳
2. 实时排行榜 zset与ziplist
3. Linux中OOM杀掉最大占用内存的进程如何从内核代码中体现

- HR面挂了
- 为啥
- 履历负分
游族网络
游戏公司
- 你履历为啥是负分
- 我有两家都没呆够一年，而且跨行业了
- 那我还想今年跑路。。。。
- 沃日，硬性待够一年啊
- 我四家都是一年
- 他说我就第一家还可以，呆了三年
- 你要不断的 百度阿里腾讯 头条美团之间跳。 HR 不会觉得你不稳定。。
- 跳槽 分 平跳，仰跳，俯跳。
- 这要看你现在的公司定位是什么。如果也是创业公司 算平跳。
- 平跳是 公司体量 业界认可度  在一个level . 比如 曾经的BAT 这三家 是平跳。我就举个例子哈。现在肯定不容易对标了。
- 仰跳的话   是你从中小公司 跳槽到 1 2线大厂。 这种跳的角度 取决于你当前公司与你想去的公司的体量差距。
俯跳 从大公司 到小公司-  一般加薪50% 起步。 但是这种也不稳定。
平跳 薪水涨幅还不错。     仰跳的薪水和级别 20%。
俯跳 肯定 50%起了。这个不用想了。
仰跳的话，看面试情况和谈的情况了。range 肯定很大。
俯跳double 的很多~~￼
- 今天被问到一个实时排行榜的问题
- redis有序集合？
- 我也是回答的Zset
- 实时排行榜 zset 标准回答了吧
- 跳表和亚索列表
- 什么时候用ziplist?
- 这个问题问到我了
- ＞某个值的时候
- 我就不问> 什么值了。这个值肯定能设置吧。
我现在用zset  。  key 是  test .  怎么知道 这个key 现在用的什么数据结构？
比如你刚提到了跳跃表和压缩列表。
![1.jpeg](https://upload-images.jianshu.io/upload_images/331298-5dc9dc253ca127b8.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![2.jpeg](https://upload-images.jianshu.io/upload_images/331298-889023946d587f53.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
我记得之前讨论过文件太大怎么办。
vi 肯定不行了。 grep 也不行
然后我们曾经提到过 文件索引。指定。
可以用dd +grep
dd  if ** bs=512 skip = 1000000 count= 100 |
还有上上次提到了oom 问题。linux 是怎么选举互杀的机制。  docker 机下面是怎么样的。
- 选最大的那个，默认￼
- 怎么着最大的？ 看看源码 怎么找的。
![Linux1](https://upload-images.jianshu.io/upload_images/331298-d5a8e32456047268.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Linux2.jpeg](https://upload-images.jianshu.io/upload_images/331298-613ae1e444a75816.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- docker 下面也是这样的吗？
- 你是说dockers内，还是dockers的宿主￼
- docker内。
- 如果不做任何现在，也应该一样，毕竟是享有一套内核，我查下去
![看编码.png](https://upload-images.jianshu.io/upload_images/331298-68cb7d022fb4e431.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
你说的看编码是这个吗

![docker.jpeg](https://upload-images.jianshu.io/upload_images/331298-fd1da8c86a34c290.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)












###操作系统
- 1.操作系统相关
- 2 Linux系统下你关注过哪些内核参数，说说你知道的。
- 3 Linux下IO模型有几种，各自的含义是什么。
- 4 epoll和poll有什么区别。
- 5 平时用到哪些Linux命令。
- 6 用一行命令查看文件的最后五行。
- 7 用一行命令输出正在运行的java进程。
- 8 介绍下你理解的操作系统中线程切换过程。
- 9 进程和线程的区别。
- 10 top 命令之后有哪些内容，有什么作用。
- 11 线上CPU爆高，请问你如何找到问题所在。
#背景
以稳定性体系建设作为指标管控

##故障分类

###代码编写

```全局map
//全局data 权限-
data.Region = map[string][]user2.RegionCityInfo{}
//不断被刷


2.panic 情况、go fun (set 数据。然后访问同一块指针。导致挂掉) .加锁问题 、Troubleshooting

3.嵌套多个 goroutine 忘记 recover

4. go 并发 使用少。导致读写冲突、比如 并发读写map
```


###资源不足
```
1.内存泄漏  https://segmentfault.com/a/1190000020103403 go oom 问题。原因是map 每次都new 没有被释放

2. 系统文件数耗尽   redis 每次都new 实例，导致tcp 端口被大量占用。引发file 句柄无法持有 发生panic

3.常见的 Goroutine 泄露所导致的 CPU、Memory 上涨等，还是得看你的 Goroutine 里具体在跑什么东西。
```




###系统设计问题

```
1. 性能开销问题
for range 一个3K+的 大map.每次都去range. QPS 过高的时候 会发生CPUIO 飙升。接口响应超时
因为range 会进行值拷贝、时间损耗在拷贝、要40+us.经过压测实践、占用cpu高。性能变低、当QPS 1000的时候。就额外开销40ms. 而 我们的QPS 1000是1000ms..当加剧的时候。我们的性能会变低、会发生抢占CPU的情况、造成开销过大

2.大量go 启动 导致 无法回收、重启大法
```


###配置问题
```
1.runtime 获取核心数问题为什么获取到错误的 cpu 数，会导致业务耗时增长这么严重？主要还是对延迟要求太敏感了，然后又是高频服务，runtime 的影响被放大了。
关于怎么解决获取正确核数的问题，目前 golang 服务的解决方式主要是两个，第一是设置环境变量 GOMAXPROCS 启动，第二是显式调用 uber/automaxprocs。
golang 是如何设置 cpu 核数，并取成宿主的核数的呢，追 runtime.NumCPU() 发现，其实现细追，发现是 getproccount, 查 linux 下源码发现，其实调用的是 sched_getaffinity，直接通过系统调用获取的宿主机核数。
https://mp.weixin.qq.com/s?__biz=MzU1ODEzNjI2NA==&mid=2247487164&idx=2&sn=594a9fee9f54660cf3b12a3ce6a2ea88&source=41#wechat_redirect

numcpu 是否一定和P一样就好 我们可以trace 看到多少个P。线程创建数 不会超过他4-5倍吧

```




[![stars](https://img.shields.io/github/stars/xiaowei520/go_interview?color=4169E1&logo=github&style=flat-square)](https://github.com/xiaowei520/go_interview/stargazers)
[![forks](https://img.shields.io/github/forks/xiaowei520/go_interview?color=4169E1&logo=github&style=flat-square)](https://github.com/xiaowei520/go_interview/network/members)
[![license](https://img.shields.io/github/license/xiaowei520/go_interview?color=4169E1&style=flat-square)](./LICENSE)

# 大厂面经

我们集中了腾讯，阿里，百度，滴滴，字节，B 站 等一线大厂的核心开发，专注分享国内一线大厂面经，让你轻松拿到 offer，直接内推。

## 我们主要做什么 ?

* 真实的大厂面经分享，让你第一时间掌握当前面试方向
* 源码解读，让你不再惧怕任何面试官刁难
* 每日算法，让你轻松通过算法筛选
* 问题答疑，轻松掌握问题精髓
* 架构分享，提升日常的架构设计能力
* 非正式开车，让你随时飚升高速列车

## 面试技巧

搜集大佬进入大厂真实经历，分享从面试前准备，到面试中灵活应答的详细细节。

1. [阿里大佬面试小技巧](./面试技巧/面试小技巧.md)

## 面经

* [京东](https://github.com/go-share-team/go_interview/tree/master/%E9%9D%A2%E7%BB%8F/%E4%BA%AC%E4%B8%9C)
* [阿里](https://github.com/go-share-team/go_interview/tree/master/%E9%9D%A2%E7%BB%8F/%E9%98%BF%E9%87%8C)
* [作业帮](https://github.com/go-share-team/go_interview/tree/master/%E9%9D%A2%E7%BB%8F/%E4%BD%9C%E4%B8%9A%E5%B8%AE)
* [滴滴](https://github.com/go-share-team/go_interview/tree/master/%E9%9D%A2%E7%BB%8F/%E6%BB%B4%E6%BB%B4)
* [腾讯](https://github.com/go-share-team/go_interview/tree/master/%E9%9D%A2%E7%BB%8F/%E8%85%BE%E8%AE%AF)
* [美团](https://github.com/go-share-team/go_interview/tree/master/%E9%9D%A2%E7%BB%8F/%E7%BE%8E%E5%9B%A2)
* [小米](https://github.com/go-share-team/go_interview/tree/master/%E9%9D%A2%E7%BB%8F/%E5%B0%8F%E7%B1%B3%E6%B8%B8%E6%88%8F)
* [映客直播](https://github.com/go-share-team/go_interview/tree/master/%E9%9D%A2%E7%BB%8F/%E6%98%A0%E5%AE%A2%E7%9B%B4%E6%92%AD)
* [游族网络](https://github.com/go-share-team/go_interview/tree/master/%E9%9D%A2%E7%BB%8F/%E6%B8%B8%E6%97%8F%E7%BD%91%E7%BB%9C)
* [目睹直播](https://github.com/go-share-team/go_interview/tree/master/%E9%9D%A2%E7%BB%8F/%E7%9B%AE%E7%9D%B9%E7%9B%B4%E6%92%AD)
* [字节](./面经/字节)
* [贝壳](./面经/贝壳)

## 日常讨论

## 如何加入我们 ?

可以通过 issue 分享你的面经。我们也有专门的微信群，里面有一大群一些大厂的开发，手把手现场教学。

可以添加微信：forever_hhf，拉你入群，备注：面试

## 公众号

欢迎关注公众号，随时卷起来

![](.img/qrcode.jpg)
1. go gc 为何是非分代的？
2. go gc 为何是非紧缩的?
3. 并发三色标记扫描是什么？
4. go 如何实现的 并发三色标记扫描？
5. 强三色不变性和弱三色不变性的含义？
6. gc是为何需要写屏障？
7. 插入写屏障和删除写屏障的时机和区别？go中如何实现的？
8. GC 的四个阶段？
9. 为何需要辅助标记和辅助清扫？
10. GC 4个阶段，STW发生在何时？
11. 描述下 gc 调步算法的实现？
12. 工作中gcdebug的使用？
13. gc 清扫阶段 对象回收 和 内存单元回收的联系和差异？**Go scheduler源码分析**

`https://segmentfault.com/a/1190000018777972`

**Go GMP 流转案例**

`1. time.sleep 阻塞时, time内部由最小堆实现。通过gopark解绑GM`

`2. 网络请求 阻塞,  网络IO 其实就是打开一个socket流。读写数据。将GR被移动到 epoll模型中。不影响当前M和P的绑定执行其他GR`

`3. 调用系统方法 比如文件IO时, GM单继续独自处理阻塞。而M和P解绑。P会切换一个新的M(如果有空闲M 获取空闲M) 绑定后 继续执行其他GR`



**GO 抢占问题**

`1.谁来负责标记抢占? sysmon 启动一个新线程。检查标记.
 sysmon函数是Go runtime启动时创建的，负责监控所有goroutine的状态，判断是否需要GC，进行netpoll等操作。sysmon函数中会调用retake函数进行抢占式调度
 1.14前不具备抢占死循环的gr. 比如 go func(){ for{} }.
`
**字典序：从小到大类似abcd这样的顺序**
> 问题：比如10pb文件全在机器a,然后给你3000台机器,你怎么调度起来这些机器，对他做排序?数据都是“jdflkajogenkngnlkjdlfjwlengp”这样的数据！
## 先将大问题分解为小问题：
1. 怎么切割文件 
2. 分发到1000台机器的策略 
3. 最后怎么合并成字典序文件

### 一、怎么切割文件 
1. 通过split
了解一下split的机制！不行，spilt会一次加载出来！  

2. 通过seek
seek可以直接定位到文件的指定位置，seek会从偏移量开始读。

3. iomap将文件映射进内存
用iomap 将文件数据映射进入内存，不要映射全部 至映射部分 然后偏移一下，然后机器ip 进行一致性hash 给到其他机器。映射进入内存的采用 从尾部寻找\r\n 进行数据行切割

### 二、分发到1000台机器的策略 
1. 通过网络传输

2.通过磁盘挂载

讨论：
假设是放到固态，每秒吞吐1000MB，10pb大概是10*1000*1000，115天加载完成。
要是通过拆硬盘的方式，三千台机器要拆多久？

### 三、最后怎么合并成字典序文件

1. 对大文件做随机采样。 来确定下hash 分布后的情况。大致算下命中率
2.seek 分割3000份  然后rsync 到 3000台机器。
3. 根据第一步的 概率 做hash. 分割 hash保证比较均匀的数据排列
4.就做典型的字典排序咯~ 然后 按照机器1-3000台。 文建索引链接就可以了。
5. 如果需要对这个文件做查询、 我们可以自建维护个 索引。 这就更有意思了。 面试官会对你刮目相看的。

参考链接：[应用访问地域排名分析](https://segmentfault.com/a/1190000020916113)

**旁路话题：单线程还是多线程读文件**
结论：单线程快！
分析：IO已经跑满了，多线程的切换反而浪费CPU时间。
**注意：**区分顺序读和随机读在采用单线程或者多线程。
### [HHF的技术博客](https://www.haohongfan.com/post/2020-03-11-go-cache/)

欢迎关注微信公众号《HHF技术博客》

- 我里面本地缓存用的go-cache, 谁知道用的有问题, 放的太多了, 把go-cache的问题引发出来了. ￼
- 啥bug
- 溢出了呗。
go cahce 不是map+读写吗
- 是的, 关键go-cache的整个一个大map, 他的定期清理map时, 会锁整个map, 如果这个map方的太多了, 就会遇到这个问题
- 他这个不合理。。
搞成分段map 是不是就更牛逼了
可以对标N核心 搞成多段map. 压力应该小很多。
- 听说bigcache, freecache是这么搞的, 不过我还没去看源码
- 我自己写的就是分段map...
没看其他源码。。。
分段map + cas +  双buff
map 更新的话，应该是 map 底层的mutex 导致的吧
- 嗯, 是的
- 因为dirty data 导致 mu.mutex
那这是 map自身的问题~ 性能有限
# 海量数据处理总结

## 案例

### 1 提取出某日访问百度次数最多的某个ip

解决方案：从访问百度的日志中逐个提取对应的`ip`（`ip`为32位的，最多有2^32个`ip`），并采用映射的方法，比如模1000，把`ip`地址映射到1000个文件中，再通过hash_map对每个小文件中`ip`出现的频率进行统计，找到出现频率最大的`ip`以及其对应的频率。最后，从这1000个频率中找到频率最大的`ip`，即为所求。

处理方法：`Hash`分而治之 + `hash_map/bit-map`(2^32 bit = 512M)

### 2 统计最热门的10个查询串

> 搜索引擎会通过日志文件把用户每次检索使用的所有检索串都记录下来，每个查询串的长度为1-255字节。
>
> 假设目前有一千万个记录（去重后，总条数不超过3百万个），请你统计最热门的10个查询串，要求使用的内存不能超过1G。

预计算：1 G = 1000 M = 1000 000 K = 1000 000 000 B / 255 = 4000 000个

解决方案：首先，由于总条数不超过3百万个，且1G可容纳4百万查询串，可用`hash_map`统计每个查询串出现的次数。然后，借助堆找出`Top K`，时间复杂度为`NlogK`。我们需要维护一个小顶堆，遍历所有字符串出现的频率，如果频率大于堆顶元素，则交换元素并调整堆，遍历完后堆中保留的查询串，即为所求。

处理方法：`hash_map `+ 堆

### 3 统计频数最高的100个词

> 有一个1G大小的文件，里面每一行是一个词，词的大小不超过16Byte，内存限制1M。返回频数最高的100个词。

预计算：1 M = 2^10 K = 2^20 B / 2^4 = 2 ^ 16个

解决方案：顺序读取文件，对于每一个词x，取`hash(x)%5000`，将所有的单词放到不同的文件中，此时每个文件大概200kb左右。（如果某个单词数量过大，文件超过1M大小，则继续按照上述方法进行切分，直到分解得到的小文件的大小都不超过1M）。**选取一个文件利用hash_map或者trie树统计每一个单词的频率，并使用长度为K的小顶堆记录当前频率前100的单词**，依次对剩余的文件按照上述策略进行处理。遍历完所有文件后，堆中的数据即为所求。

处理方法：`hash`分而治之 + `hash_map/trie`树 + 堆

### 4 大文件数据排序

> 有10个文件，每个1G，每个文件的每一行存放的都是用户的`query`，且每一个文件的`query`都可能重复，须要按照query的频率进行排序。

解决方案：顺序读取10个文件，按照`hash(query)%10`的结果将`query`写入到另外10个文件中。这样新生成的文件每个的大小约为1G。此时需要一台内存在2G左右的机器，依次对用`hash_map(query, query_count)`来统计每个`query`出现的次数。利用快速/堆/归并排序按照出现次数进行排序。将排序好的query和对应query输出到文件中，这样就得到了10个排好序的文件。最后对这10个文件进行归并排序（外排序，多路归并排序）。

处理方法：`hash`分而治之 + `hash_map` + 堆 + 外排序

### 5 找出大文件的交集

> 给定a、b两个文件，各存放50亿个`url`，每个`url`各占64字节，没存限制4G，找出a、b文件共同的`url`？

解决方案1：分而治之。遍历文件a，对每个url求取`hash(url)%1000`，然后根据所取得的值将url分别存储到1000个小文件中（记为`a0，a1，...，a999`）。每个小文件大约`300M`。

遍历文件b，采取相同hash策略，将b文件拆分为1000小文件（记为`b0，b1，...，b999`）。

上述处理后，所有可能相同的url都在对应的小文件（`a0vsb0，a1vsb1，...，`）中，不对应的小文件不可能有相同的url。最后针对每组小文件找出相同的url即可。

解决方案2：`Bloom filter`（有一定的错误率）。4G内存（4 000 000 000 * 8 = 320亿bit）。将其中一个文件中的`url`使用`Bloom filter`映射到`bitmap`上。然后，遍历另一个文件的`url`，检查是否存在即可。

处理方法：`hash`分而治之 + `hash_map`，`Bloom filter`（有一定的错误率）

### 6 找出不重复的数字

> 在2.5亿个整数中找出不重复的整数，注，内存不足以容纳这2.5亿个整数。

解决方案1：分而治之。将这2.5亿个整数按照一定的hash策略，分配到内存可容忍的大小，并找出每一个文件中的不重复数字。

解决方案2：`2-BitMap`。消耗内存2^32 * 2 / 8 = 1G左右内存。其中每个数分配2bit，00表示不存在，01表示出现过一次，10表示多次。然后扫描这2.5亿个整数，查看`bitmap`中相对应位，如果是00变01，01变10，10保持不变。最后，遍历bitmap进行数据输出即可。

处理方法：`hash`分而治之，`2-bitmap`

### 7 判断数字是否存在

> 给40亿个不重复的`unsigned int`的整数，没排过序，然后再给一个数，如何快速判定这个数是否存在？

解决方案1：`BitMap`。用1bit表示数值是否存在，需要内存2^32 / 8 = 512M内存。其中一个bit代表一个`unsigned int`的值，读入40亿个数，设置相应的bit位，读入要查询的数，查看相应的bit位是否为1，为1则表示存在，为0则表示不存在。（桶排序的思想）

解决方案2：分而治之。按照最高位的0 1进行拆分文件。判定目标数最高位是0还是1，然后确定目标位于哪个文件中。再判定次高位，并接着进入相应的文件中再查找...以此类推，就可以查找到目标了。相当于二分查找。

处理方法：`bit-map`，二分思想-分而治之

### 8 多机器分布`TOP`问题

> 海量数据分布在100台电脑上，如何高效统计这批数据的`TOP10`。

解决方案：针对每台机器利用小顶堆找出`top10`。汇总所有机器的数据，总数1000个，再次利用小顶堆，遍历数值，即为所求。

处理方法：`mapreduce`+堆

## 处理方法总结

### 1 `Bloom Filter`

适用范围：（有一定的错误率）可以用来实现数据字典、数据的判重、求交集。

### 2 `Hashing`

适用范围：快速查找，通常需要将全部数据加载到内存。

碰撞处理：链地址法；开放定址法；双重`hash`法

### 3 `Bit-map`

适用范围：可进行数据的快速查找，判重，删除，一般来说数据范围是int的10倍以下

基本原理及要点：适用`bit`数组标识某些元素是否存在

### 4 堆

适用范围：海量数据前n大，并且n比较小，堆可以放入到内存中。

### 5 双层桶划分

适用范围：第k大，中位数

基本原理及要点：因为元素范围很大，不能利用直接寻址表，所以通过多次划分，逐步确定范围，最后在一个可以接受的范围内运行。

### 6 数据库索引（B+树）

适用范围：大数据量的增删改查

### 7 倒排索引

适用范围：搜索引擎，关键字查询

基本原理及要点：一种索引方法，被用来存储在全文搜索下某个单词在一个文档或一组文档中的存储位置的映射。

> 以英文为例，下面是要被索引的文本： `T0 = "it is what it is" T1 = "what is it" T2 = "it is a banana"  `
>
> 我们就能得到下面的反向文件索引：
>
> `	"a": {2} "banana": {2} "is": {0, 1, 2} "it": {0, 1, 2} "what": {0, 1}`
>
> 检索的条件"what","is"和"it"将对应集合的交集。  

问题实例：文档检索系统，查询那些文件包含了某单词，比如关键词搜索。

### 8 外排序

适用范围：大数据排序，去重

基本原理及要点：外排序的归并方法，置换选择败者树原理，最优归并树。

### 9 `Trie`树

适用范围：数据量大，重复多，但是数据种类小可以放入内存

基本原理及要点：实现方式，节点孩子的标识方法

扩展：**压缩实现**

### 10 分布式处理 `mapreduce`

适用范围：数据量大，但是数据种类小可以放入内存

基本原理及要点：将数据交给不同的机器去处理，数据划分，结果归约

> 经典问题分析：
>
> 　　上千万or亿数据（有重复），统计其中出现次数最多的前N个数据,分两种情况：可一次读入内存，不可一次读入。
>
> 　　可用思路：`trie`树+堆，数据库索引，划分子集分别统计，`hash`，分布式计算，近似统计，外排序
>
> 　　所谓的是否能一次读入内存，**实际上应该指去除重复后的数据量**。如果去重后数据可以放入内存，我们可以为数据建立字典，比如通过 `map`，`hash_map`，`trie`，然后直接进行统计即可。当然在更新每条数据的出现次数的时候，我们可以利用一个堆来维护出现次数最多的前N个数据，当然这样导致维护次数增加，不如完全统计后在求前N大效率高。
>
> 　　如果数据无法放入内存。一方面我们可以考虑上面的字典方法能否被改进以适应这种情形，可以做的改变就是将字典存放到硬盘上，而不是内存，这可以参考数据库的存储方法。
>
> 　　当然还有更好的方法，就是可以采用分布式计算，基本上就是`map-reduce`过程，首先可以根据数据值或者把数据`hash(md5)`后的值，将数据按照范围划分到不同的机子，最好可以让数据划分后可以一次读入内存，这样不同的机子负责处理各种的数值范围，实际上就是`map`。得到结果后，各个机子只需拿出各自的出现次数最多的前N个数据，然后汇总，选出所有的数据中出现次数最多的前N个数据，这实际上就是`reduce`过程。
>
> 　　实际上可能想直接将数据均分到不同的机子上进行处理，这样是无法得到正确的解的。因为一个数据可能被均分到不同的机子上，而另一个则可能完全聚集到一个机子上，同时还可能存在具有相同数目的数据。比如我们要找出现次数最多的前100个，我们将1000万的数据分布到10台机器上，找到每台出现次数最多的前 100个，归并之后这样不能保证找到真正的第100个，因为比如出现次数最多的第100个可能有1万个，但是它被分到了10台机子，这样在每台上只有1千个，假设这些机子排名在1000个之前的那些都是单独分布在一台机子上的，比如有1001个，这样本来具有1万个的这个就会被淘汰，即使我们让每台机子选出出现次数最多的1000个再归并，仍然会出错，因为可能存在大量个数为1001个的发生聚集。因此不能将数据随便均分到不同机子上，而是要根据hash 后的值将它们映射到不同的机子上处理，让不同的机器处理一个数值范围。
>
> 　   而外排序的方法会消耗大量的IO，效率不会很高。而上面的分布式方法，也可以用于单机版本，也就是将总的数据根据值的范围，划分成多个不同的子文件，然后逐个处理。处理完毕之后再对这些单词的及其出现频率进行一个归并。实际上就可以利用一个外排序的归并过程。  先简单做个自我介绍：
本⼈是⼯作多年的⼀枚'⽼码农'，对⽐群⾥的年轻朋友们，是有点'⽼'哈，最近⼯作是在⼀家视频⾏业的互联⽹公司⼯作6年以上。大家可能觉得，这个时期出来看机会，是往⽕坑⾥跳吗？

个⼈不这么认为，借此疫情的特殊时期，通过企业市场招聘情况也能帮你把握⼀下⼤致业务发展⽅向，这其实是好事。

当然了，招聘岗位有限，狼多⾁少，岗位竞争肯定⽐往年更加激烈的，所以需要珍惜每⼀个机会。

⽐如在新浪的⼀位朋友也很缺⼈，内部还在商讨，少量HC还没有正式放开...本次薪资涨幅还算满意，具体就不便透露了。在谈薪资⽅⾯，如果你能提前拿到其他公司Offer，且你之前Base还可以，那么还是容易谈的。

⾯试结果：
京东、美团⾯试已通过。具体定级对标P7上下这样⼦吧，个⼈理解是资深、专家岗，因为进去也基本都是⼲活的，带⼈与否看情况。

⾯试流程：
京东、美团多个部⻔都是并⾏⾯试的，这个没有正式谈薪前都是没问题的。
京东：三 / 四⾯技术 + HRBP美团：四⾯技术 + HRBP + 专职HR
个⼈感受美团HR专业性、与候选⼈沟通⽅⾯体验相对更好些，因招⼈旺季，HR也都很忙。
HR职责也会细分，联系协调⾯试的有专⻔的HR、技术⾯过会HRBP⾯试，最后都通过确定职级，然后会有专职HR谈薪资。
⽽京东HR...，这⽅⾯相对美团吐槽也更多⼀点⼉，所以后⾯同学去引起注意即可，如果你是「不确定」的候选⼈，各种情况可能都会遇到，⼤家知道就好。


### 京东⾯面试过程总结
- ⾯试方式:电话⾯试，他们都不露脸呀！好处是不⽤⼿撸代码了。
京东物流：三轮或四轮技术⾯ 
通常⼀或⼆⾯是研发同学（即未来跟你⼀起⼯作的同事）、⼆或三⾯（架构师T7或T8，要给你定级的）、三或四⾯（部⻔负责⼈，也会有技术⾯）。你的定级是在最后两⾯综合来确定的。

####【京东⼀或⼆⾯】
因为⼀⾯或⼆⾯都是研发同学。 基本都是常⻅的⼀些⾯试题，也不会很深，群⾥同学肯定都会。个⼈在部⻔内曾推⼴过 Apollo 配置中⼼，⾯试官也会让你说出实现原理。
京东内部应该是不使⽤ Dubbo 的，他们的RPC框架叫做「JSF」，内部实现机制 应该是跟 Dubbo 很像。 所以京东⾯试官要问RPC，基本都问到 Dubbo 实现原理，内部扩展机制等等，肯定不会问 JSF 了，也没开源。
####【京东架构师⾯】 
项⽬问的很详细，说的我很累~ 基本每个项⽬具体实现⽅案、亮点都讲了⼀下，遇到的问题。

####具体技术⾯：
1. JVM & GC 1）你们JVM线上使⽤的什么垃圾回收算法？CMS、G1 
2. 详细说⼀下CMS垃圾回收算法回收过程？4个阶段
3. CMS中的remark阶段⽐较慢，怎么分析和解决？ 
- XX: +CMSScavengeBeforeRemark Remark前提前来⼀次YGC；增加CMS回收的线程数...
4. 线上如何来分析GC问题？
5. JVM线上是如何调优的？
6. 1.7和1.8中jvm内存结构的区别？
7. 发⽣Full GC时会回收Metaspace空间内存吗？

#### 2、数据库
1. 锁等待问题如何处理？⼀个事务中调⽤了本地事务，还调⽤了RPC接⼝？
我说这⾥涉及了分布式事务了，他说不⽤，把RPC接⼝调⽤放在最后⾯...然⽽实际逻辑并不⼀定都这样的...他应该是想问出现死锁、间隙锁问题应该如何来处理，说了下死锁的解决思路，没有太get到点上。

3. 缓存 & ⾼并发
1）⾼并发下缓存与数据库⼀致性如何保证？
2）⾼并发情况下扣减库存如何处理？如何优化？参考⽯杉架构笔记⾥的⽂章
3）当队列满了，核⼼线程空闲、扩展线程也空闲，谁从队列⾥获取？
4. JUC 1）为什么线程池⾥要先创建coreSize⼤⼩的核⼼线程？2）线程池中的各个参数的含义解释⼀下？

5. 框架 & 源码
1）SpringCloud和RPC具体都有哪些区别？
2）SpringCloud中遇到过哪些坑，具体降级了Eureka注册中⼼的坑。
3)最近⼀分钟上报的次数计算错误，bug 
4)⾃我保护机制，剔除Down掉服务器并不是90秒，也是个bug，⾄少180秒以上。

6. 算法题
1）编程珠玑⾥的⼀道题：⼀个40亿的⽆序数字，请找出不存在的数字
2)⼀个很⼤的List，⾥⾯都是int类型，如何实现加和？因为电⾯，就说了⼀下思路

### 京东四⾯部⻔领导
1. 介绍下项⽬的亮点，有成就感的项⽬

2. JVM相关深⼊
1）深⼊探讨了CMS、GC Roots，跨代是如何进⾏GC的？有点发散，提到了GC Roots Tracing，Rset、Card Table 
2）ParallelScavenge了解吗，什么算法？实际是复制算法【⽼年代可以搭配Serial Old或者Parallel Old】
3）为什么不能和CMS⼀起使⽤呢？？后来听说这个负责⼈jvm源码他都看过，研究很深。

3. MySQL索引如何做优化的？
1）假设有⼀个表字段⼏⼗个，索引如何创建的？所有字段都能建吗？区分度、选择性、列基数
2）MySQL为什么是B + 树的结构，为什么不能是红⿊树呢？优化的是什么，优化的是磁盘IO，减少磁盘寻址。

4. MQ队列⽤了哪些，Kafka，那⾦融场景下，Kafka如何保证消息不丢失？ack = -1，Leader-->ISR写⼊所有的follower 5、Redis⽤过哪些数据结构？zset底层是什么结构，hash和跳表，为什么是跳表，查找效率⾼。

6. 其他
1）⾃身优缺点？
2）⼀句话总结下⾃⼰？
3）对待加班的看法？
### 作业帮(一面)

1. channel实现
2. go调度原理
3. select和epoll的区别
4. redis分布式锁
5. redis缓存穿透,缓存雪崩
6. 时序数据库如何实现
7. raft选举,高可用
8. kafka的offset怎么管理
9. kafka多个consumer group 消费同一个 partition 有问题吗？为什么同一个 consumer group 里不能消费同一个partition
10. 数据库中的乐观锁悲观锁
11. 程序中的乐观锁和悲观锁
12. 二叉搜索树,两个节点被交换了位置,怎么恢复
13. 消息队列消费端的推和拉有什么区别
14. 长链接转短链接设计

### 作业帮(二面)

- 前端gateway集群维护客户端长连接,gateway分发请求到后端集群处理逻辑后,如何保证后端处理完毕回包回到正确的gateway机器
- 承接第一个问题,如果机器挂掉,如何最小损失情况下恢复,k8s集群

3. 实习生+老同事都有问题需要你处理,怎么办
# Go-2年社招-202008.md

## 后台研发-Golang

### 1M

* 负责的项目整体架构,负责了哪一个部分，各个组件之间的交互，项目中Redis分布式锁怎么实现的
* 怎么样理解Go的协程？Go协程的调度？
* Redis 一般你们用来做什么，Redis 有几种数据结构，有什么使用场景，Redis的高可用了解过么
* Redis分布式锁底层实现，分布式锁羊群效应听说过么
* 数据库MySQL和MongoDB怎么选择,MySQL 和 MongoDB 区别
* TCP和UDP的区别,TCP三次握手？SEQ 是干什么的？两次握手行不行？　TCP Fast Open了解么,TCP的拥塞控制是怎么做的
* HTTP协议的发展,HTTP缓存实现方式，跟HTTP缓存相关的头部字段，HTTP 302了解么
* HTTP和HTTPS有什么区别、HTTPS的访问流程、CA中心是什么，确定加密协议过程
* DSN 查询过程,递归查询和迭代查询的区别
* Kafka如何保证消息可靠,Kafak为什么那么快
* Dos攻击了解么
* 设计一个高并发、高可用的架构
  * 读比较多怎么设计
  * 写比较多怎么设计
  * 服务的负载均衡怎么做
  * MySQL 怎么部署，读写分离怎么做
  * MySQL索引是什么,什么数据结构，MyISAM与InnoDB索引的区别，假设有一张表，有a,b两个索引，索引会怎么选择？(a,b) 是联合索引时会怎么选择
  * MySQL的事物，有什么特征，ACID是怎么实现的，隔离级别有几种，MVCC是怎么实现的，乐观锁，悲观锁
  * 缓存的热Key和大Key怎么处理
  * 简单说说kafka的架构，我要实现消息的顺序消费怎么办
* 说说CDN的架构，缓存的实现, 用户的访问流程，调度是怎么选择机房的
* 算法题目: 两个有序数组合并
* 算法题目: 二叉数中序遍历（非递归实现）
* 算法题目: 寻找K值
* 算法题目: 岛屿数量
* 算法题目: 岛屿数量

### 2M

* 项目:说说定时任务系统怎么做的，什么是一个任务，一个任务的生命周期是什么样的
* 现在你的项目Redis分布式锁有什么问题，怎么解决
* Redis分布式锁和数据库排他锁有什么相同、区别
* 场景题: 不用定时任务的方式，实现一条消息定时发送或者任务到点执行
* 算法题目: 两个有序数组合并，返回合并之后的中位数
* 算法题目: 字符串之间保留一个空格之后将字符串翻转
* 说说在工作中遇到比较困难的事情

### 3M

* 实现MapReduce中的Map,例如: cat file.txt | ./go-reader　-cmd "${cmd}
  " > result.txt
  * go-reader为接受cat file.txt的输入lines，将lines给${cmd}处理，最后输出结果
  * 进程间怎么通信，go-reader要考虑协程池吗，为什么
* 你负责的系统中，你一般关注哪些点，怎么保证你关注的点不出问题
* 编码过程中你比较注意哪一些
  * golang的panic怎么理解，怎么处理，recover 一般怎么处理
* 实现一个通用的排序方法，怎么实现
  * 接口怎么设计，需要实现方实现什么
  * 怎么调用这个通用的排序方法1-2面 100分钟
1. mysql .索引相关.select * from table  where id=1 或 where a=1  数据是怎么查出来的（a不同索引的情况）
2. update set a=1 from table where a=2 会加锁吗。怎么加的。会锁表吗 。如果索引很大。超过rows 20%-50% .会全表?
3. redolog undolog binlog 的概念
4. mysql主从怎么实现的
5. go 调度模型。发生网络io.会怎么调度。发生阻塞的IO会怎么调度。epoll
6. go gc问题
7. go channel 怎么实现的。 
8. 实现一个并发模型。生产者消费者
9. 既然你提到了gearman.有其他的队列可以替代吗？比如？可以举例吗？
10. redis 的删除策略。定时 定期 惰性 lru
11. redis 各类型的底层数据结构。
12. etcd 怎么保持一致性的? 脑裂怎么解决的？
13. 你对分布式怎么看？可以举个例子吗？
14. 线上突然大量502.怎么排查
15. 架构设计-微信朋友圈
16. 线上熔断降级怎么做的
17. 描述 推送架构设计
18. 讲一下open-falcon的架构设计吧。metrics 采集标准?
19. 为什么你们把php换成go ?优点？为什么单机 go 的吞吐比php 高?原因是?
20. 举几个例子 说明下 代码优化的地方？提升的原因是什么？
21. 你怎么评估重构业务。重构的标准是什么？怎么灰度切换？
24. 你对自己未来的规划是怎么样子的？

3面 - 60分钟
1. C++了解吗?
2. 虚拟地址和位地址怎么映射？
3. C++段错误是什么情况引起的？
4. Go runtime 了解吗?讲一下调度。发生文件IO的时候 G 怎么调度的？
5. 有10亿用户，让你设计一个社区架构。包括点赞 发帖 删帖 的积分架构、期间一直在追问设计合不合理
6. 给你1亿个URL 。爬取信息。会遇到什么问题？从 CPU 磁盘 网络 等方面。这个聊了很多。
7. 平常喜欢看什么书？想往管理层走 还是技术路线。
- Redis MySQL 数据一致性 
- 讲讲乐观锁 、悲观锁  
- Redis 热key ，有什么问题，要注意什么，如何解决
- Redis 大 key，有什么问题，要注意什么，如何解决
- PHP常用数组方法
- PHP 的array_merge和+的区别，array_merge 自己实现
- PHP 遍历比较
- PHP扩展 自己写过扩展吗
- 线程和进程的区别，golang 的协程了解吗
- Io模型 
- Io多路复用 区别
- TCP 四次挥手 
- 常见Http状态码，499什么含义，分析一下系统 502 和504 可能的原因
- Php composer 原理，性能
- Php Self static 区别
- Php错误处理 怎么提高系统健壮性
- 讲一下Golang 空结构体
- golang new make 
- golang 的map 插入顺序和输出顺序是一样的吗？随机输出
- nginx中 break、last 区别
- php的生命周期中关键四个调用: MINT -> RINT -> RSHUTDOWN -> MSHUTDOWN
- 幂等 业务处理
- 事物特性
- Redis  memcache 区别 
- 如何实现分布式锁，Redis 具体如何实现锁
- Nginx 两台前端机，多台服务机，各机器上nginx 如何配置，共享屏幕，直接演示
- PHP 框架知道哪些
- Mysql 锁模式
- 讲一讲 PHP Go python 三种语言，该如何选择
- 为什么重构，重构的意义在哪里
- Mysql 和mongo 索引底层结构
- Db的事务性底层实现原理 
- 算法 无序数组找中位数 
- 算法 两个字符串最长公共子串
- 算法 多数组 笛卡尔积，例：['s',['a', 'b'], [1, 2], 'x']，输出：[["s","a",1,"x"],["s","a",2,"x"],["s","b",1,"x"],["s","b",2,"x"]]
- 算法 二叉树中序遍历非递归
- 算法 链表奇递增 偶递减，整体递增
- 算法 两个数组，例：source=[4,0,2,0,3,0],target=[4,2,3,0,0,0]要求时间o(n)，空间o(1)
- 设计题：设计一个短连（一个长的url地址，转为一个短的）
- 设计题：亿级帖子，对应的评论表如何设计
- 设计题：全站Push 的 运营误操作如何防止

