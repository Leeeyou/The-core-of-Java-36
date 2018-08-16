# Java核心36讲

这里是将极客时间上杨晓峰老师的专栏文章，在这里整理出来方便自己后续复习用（仅用于学习）。文章分为5大模块：

> Java 基础：我会围绕 Java 语言基本特性和机制，由点带面，让你构建牢固的 Java 技术工底。  
> Java 进阶：将围绕并发编程、Java 虚拟机等领域展开，助你攻坚大厂 Java 面试的核心阵地。  
> Java 应用开发扩展：从数据库编程、主流开源框架、分布式开发等，帮你掌握 Java 开发的十八般兵器。  
> Java 安全基础：让你理解常见的应用安全问题和处理方法，掌握如何写出符合大厂规范的安全代码。  
> Java 性能基础：你将掌握相关工具、方法论与基础实践。

作为Android开发人员来说，有部分内容更多偏向后台开发，有点难度，对于这部分内容，我自己是量力而行。很多知识点以前只是听说、或者是了解概念，这次在专栏中，收获满满；只要用心读，保持清晰的头脑，应该还会有进一步的提升。

技术之外，杨老师在文章中有意的提到了很多思想层面的东西，个人觉得对于意识的提升非常有帮助（我不会告诉你意识的提升这个词是王者荣耀里面学到的）。比如说“你需要尽量表现出自己的思维深入并系统化”、“知其然并知其所以然，明白基本组成和机制”、“应用场景是我们在选择哪个API前需要考虑的，不能一概而论的说A比B绝对好”等等。当然意识层面的提高也绝非说说而已，还需结合实践和自己的思考不断摸索和总结。

在某些文章中，我添加了一些自己不太熟悉的内容介绍，认为需要补充的，就会在相应的章节里面做好记录，也算是查漏补缺，消除自己的疑虑吧。

* [开篇词 \| 以面试题为切入点，有效提升你的Java内功](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/kai-pian-ci-yi-mian-shi-ti-wei-qie-ru-dian-ff0c-you-xiao-ti-sheng-ni-de-java-nei-gong.md)
* [第1讲 \| 谈谈你对Java平台的理解？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-1-jiang-tan-tan-ni-dui-java-ping-tai-de-li-jie-ff1f.md)
* [第2讲 \| Exception和Error有什么区别？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-2-jiang-exception-he-error-you-shi-yao-qu-bie-ff1f.md)
* [第3讲 \| 谈谈final、finally、 finalize有什么不同？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-3-jiang-tan-tan-final-finally-finalize-you-shi-yao-bu-tong-ff1f.md)
* [第4讲 \| 强引用、软引用、弱引用、幻象引用有什么区别？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-4-jiang-qiang-yin-yong-3001-ruan-yin-yong-3001-ruo-yin-yong-3001-huan-xiang-yin-yong-you-shi-yao-qu-bie-ff1f.md)
* [第5讲 \| String、StringBuffer、StringBuilder有什么区别？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-5-jiang-string-stringbuffer-stringbuilder-you-shi-yao-qu-bie-ff1f.md)
* [第6讲 \| 动态代理是基于什么原理？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-6-jiang-dong-tai-dai-li-shi-ji-yu-shi-yao-yuan-li-ff1f.md)
* [第7讲 \| int和Integer有什么区别？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-7-jiang-int-he-integer-you-shi-yao-qu-bie-ff1f.md)
* [第8讲 \| 对比Vector、ArrayList、LinkedList有何区别？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-8-jiang-dui-bi-vector-arraylist-linkedlist-you-he-qu-bie-ff1f.md)
* [第9讲 \| 对比Hashtable、HashMap、TreeMap有什么不同？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-9-jiang-dui-bi-hashtable-hashmap-treemap-you-shi-yao-bu-tong-ff1f.md)
* [第10讲 \| 如何保证集合是线程安全的? ConcurrentHashMap如何实现高效地线程安全？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-10-jiang-ru-he-baozheng-ji-he-shi-xian-cheng-an-quan-76843f-concurrenthashmap-ru-he-shi-xian-gao-xiao-di-xian-cheng-an-quan-ff1f.md)
* [第11讲 \| Java提供了哪些IO方式？ NIO如何实现多路复用？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-11-jiang-java-ti-gong-le-na-xie-io-fang-shi-ff1f-nio-ru-he-shi-xian-duo-lu-fu-yong-ff1f.md)
* [第12讲 \| Java有几种文件拷贝方式？哪一种最高效？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-12-jiang-java-you-ji-zhong-wen-jian-kao-bei-fang-shi-ff1f-na-yi-zhong-zui-gao-xiao-ff1f.md)
* [第13讲 \| 谈谈接口和抽象类有什么区别？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-13-jiang-tan-tan-jie-kou-he-chou-xiang-lei-you-shi-yao-qu-bie-ff1f.md)
* [第14讲 \| 谈谈你知道的设计模式？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-14-jiang-tan-tan-ni-zhi-dao-de-she-ji-mo-shi-ff1f.md)
* [第15讲 \| synchronized和ReentrantLock有什么区别呢？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-15-jiang-synchronized-he-reentrantlock-you-shi-yao-qu-bie-ni-ff1f.md)
* [周末福利 \| 谈谈我对Java学习和面试的看法](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/zhou-mo-fu-li-tan-tan-wo-dui-java-xue-xi-he-mian-shi-de-kan-fa.md)
* [第16讲 \| synchronized底层如何实现？什么是锁的升级、降级？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-16-jiang-synchronized-di-ceng-ru-he-shi-xian-ff1f-shi-yao-shi-suo-de-sheng-ji-3001-jiang-ji-ff1f.md)
* [第17讲 \| 一个线程两次调用start\(\)方法会出现什么情况？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-17-jiang-yi-ge-xian-cheng-liang-ci-diao-yong-start-fang-fa-hui-chu-xian-shi-yao-qing-kuang-ff1f.md)
* [第18讲 \| 什么情况下Java程序会产生死锁？如何定位、修复？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-18-jiang-shi-yao-qing-kuang-xia-java-cheng-xu-hui-chan-sheng-si-suo-ff1f-ru-he-ding-wei-3001-xiu-fu-ff1f.md)
* [第19讲 \| Java并发包提供了哪些并发工具类？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-19-jiang-java-bing-fa-bao-ti-gong-le-na-xie-bing-fa-gong-ju-lei-ff1f.md)
* [第20讲 \| 并发包中的ConcurrentLinkedQueue和LinkedBlockingQueue有什么区别？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-20-jiang-bing-fa-bao-zhong-de-concurrentlinkedqueue-he-linkedblockingqueue-you-shi-yao-qu-bie-ff1f.md)
* [第21讲 \| Java并发类库提供的线程池有哪几种？ 分别有什么特点？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-21-jiang-java-bing-fa-lei-ku-ti-gong-de-xian-cheng-chi-you-na-ji-zhong-ff1f-fen-bie-you-shi-yao-te-dian-ff1f.md)
* [第22讲 \| AtomicInteger底层实现原理是什么？如何在自己的产品代码中应用CAS操作？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-22-jiang-atomicinteger-di-ceng-shi-xian-yuan-li-shi-shi-yao-ff1f-ru-he-zai-zi-ji-de-chan-pin-dai-ma-zhong-ying-yong-cas-cao-zuo-ff1f.md)
* [第23讲 \| 请介绍类加载过程，什么是双亲委派模型？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-23-jiang-qing-jie-shao-lei-jia-zai-guo-cheng-ff0c-shi-yao-shi-shuang-qin-wei-pai-mo-xing-ff1f.md)
* [第24讲 \| 有哪些方法可以在运行时动态生成一个Java类？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-24-jiang-you-na-xie-fang-fa-ke-yi-zai-yun-xing-shi-dong-tai-sheng-cheng-yi-ge-java-lei-ff1f.md)
* [第25讲 \| 谈谈JVM内存区域的划分，哪些区域可能发生OutOfMemoryError?](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-25-jiang-tan-tan-jvm-nei-cun-qu-yu-de-hua-fen-ff0c-na-xie-qu-yu-ke-neng-fa-sheng-outofmemoryerror.md)
* [第26讲 \| 如何监控和诊断JVM堆内和堆外内存使用？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-26-jiang-ru-he-jian-kong-he-zhen-duan-jvm-dui-nei-he-dui-wai-nei-cun-shi-yong-ff1f.md)
* [第27讲 \| Java常见的垃圾收集器有哪些？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-27-jiang-java-chang-jian-de-la-ji-shou-ji-qi-you-na-xie-ff1f.md)
* [第28讲 \| 谈谈你的GC调优思路?](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-28-jiang-tan-tan-ni-de-gc-diao-you-si-8def3f.md)
* [第29讲 \| Java内存模型中的happen-before是什么？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-29-jiang-java-nei-cun-mo-xing-zhong-de-happen-before-shi-shi-yao-ff1f.md)
* [第30讲 \| Java程序运行在Docker等容器环境有哪些新问题？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-30-jiang-java-cheng-xu-yun-xing-zai-docker-deng-rong-qi-huan-jing-you-na-xie-xin-wen-ti-ff1f.md)
* [第31讲 \| 你了解Java应用开发中的注入攻击吗？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-31-jiang-ni-le-jie-java-ying-yong-kai-fa-zhong-de-zhu-ru-gong-ji-ma-ff1f.md)
* [第32讲 \| 如何写出安全的Java代码？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-32-jiang-ru-he-xie-chu-an-quan-de-java-dai-ma-ff1f.md)
* [第33讲 \| 后台服务出现明显“变慢”，谈谈你的诊断思路？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-33-jiang-hou-tai-fu-wu-chu-xian-ming-xian-201c-bian-man-201d-ff0c-tan-tan-ni-de-zhen-duan-si-lu-ff1f.md)
* [第34讲 \| 有人说“Lambda能让Java程序慢30倍”，你怎么看？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-34-jiang-you-ren-shuo-201c-lambda-neng-rang-java-cheng-xu-man-30-bei-201d-ff0c-ni-zen-yao-kan-ff1f.md)
* [第35讲 \| JVM优化Java代码时都做了很么？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-35-jiang-jvm-you-hua-java-dai-ma-shi-du-zuo-le-shi-yao-ff1f.md)
* [第36讲 \| 谈谈MySQL支持的事务隔离级别，以及悲观锁和乐观锁的原理和应用场景？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-36-jiang-tan-tan-mysql-zhi-chi-de-shi-wu-ge-li-ji-bie-ff0c-yi-ji-bei-guan-suo-he-le-guan-suo-de-yuan-li-he-ying-yong-chang-jing-ff1f.md)
* [第37讲 \| 谈谈Spring Bean的生命周期和作用域？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-37-jiang-tan-tan-spring-bean-de-sheng-ming-zhou-qi-he-zuo-yong-yu-ff1f.md)
* [第38讲 \| 对比Java标准NIO类库，你知道Netty是如何实现更高性能的吗？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-38-jiang-duibi-java-biao-zhun-nio-lei-ku-ff0c-ni-zhi-dao-netty-shi-ru-he-shi-xian-geng-gao-xing-neng-de-ma-ff1f.md)
* [第39讲 \| 谈谈常用的分布式ID的设计方案？Snowflake是否受冬令时切换影响？](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/di-39-jiang-tan-tan-chang-yong-de-fen-bu-shi-id-de-she-ji-fang-an-ff1f-snowflake-shi-fou-shou-dong-ling-shi-qie-huan-ying-xiang-ff1f.md)
* [结束语 \| 技术没有终点](https://github.com/LeeeYou/The-core-of-Java-36/blob/master/jie-shu-yu-ji-zhu-mei-you-zhong-dian.md)



