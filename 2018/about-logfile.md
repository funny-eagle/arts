# 关于日志的那些事儿


这篇短文整理了关于日志的一些内容，耗叔要求的【目标是一篇“引经据典“的，“全面系统“的对日志的介绍】。结合一些参考链接和以往的经验，主要对软件开发过程中日志相关的知识进行梳理和学习。

## 日志的定义

先说说咱们日常生活中的日志，就是日记的另一种说法。

> [日记](https://zh.wikipedia.org/wiki/%E6%97%A5%E8%AE%B0)是以日期为排列顺序的笔记。人们用日记来记录天气、事件一直到个人的心理感受、思想深处。日记可以是记录将要做的事情的，也可以记录已经发生的事情和心情。

在计算机领域，日志文件的定义如下：

> [日志文件](https://zh.wikipedia.org/zh-hans/%E6%97%A5%E5%BF%97%E6%96%87%E4%BB%B6)(logfile)是一个记录了发生在运行中的操作系统或其他软件中的事件的文件，或者记录了在网络聊天软件的用户之间发送的消息。日志记录(Logging)是指保存日志的行为。


## 日志的意义

记录日志是软件开发中很重要的一部分。编写良好的日志代码能为软件提供快速的诊断信息和良好的存储结构。我们可以通过对记录的日志进行分析来支撑一些其他的功能，例如解析mysql的binary log来同步数据，通过日志分析用户行为，系统某功能的访问频率，通过日志排查程序的BUG等等。

- 开发人员调试程序
	- 开发阶段的debug日志，通过输出重要参数来调试跟踪程序的正确性
- 记录用户行为
	- 记录用户的操作，访问，一般用于数据分析，操作留痕 
- 记录程序的运行情况
	- 记录程序的运行情况，尤其是异常情况，是排查问题的重要线索
- 记录系统的状态
	- 记录网络请求、IO等，便于监控和排查问题
	
## 日志的类型

- 行为日志
	- 分析用户的行为日志内容来实现数据分析等功能 
- 访问日志
	- 访问来源、功能的访问频率 
- 错误日志
	- 记录程序运行错误的现场，解决问题的重要线索
- 数据库日志
	- 记录SQL记录，可以分析慢SQL，生成报告 
- ...

## 日志的格式

每条日志都要记录发生的时间、级别、日志内容、错误码、以及对应代码的位置，存储在文本文件或者数据库中。
	
- JSON
	```
	{
  		"@timestamp": "2017-07025 17:02:12",
  		"level": "error",
  		"message": "connection refused",
  		"service": "listener",
  		"thread": "125",
  		"customerid": "776622",
  		"ip": "34.124.233.12",
  		"queryid": "45"
  	}
	```
- 键值对
	```
		2017-07025 17:02:12 level=error message="connection refused"
service="listener" thread=125 customerid=776622 ip=34.124.233.12
queryid=45
	```

## 日志的级别

不同的日志框架对日志的分类略有差异，通常来说，日志可以被分为以下六个级别，通过配置来输出对应级别的日志

- FATAL
	- 导致程序提前结束的严重错误。
- ERROR
	- 运行时异常以及预期之外的错误。
- WARN
	- 预期之外的运行时状况，不一定是错误的情况。
- INFO
	- 运行时产生的事件。
- DEBUG
	- 与程序运行时的流程相关的详细信息。
- TRACE
	- 更加具体的详细信息。

## 日志归档

根据天数或者文件大小产生新的文件，对日志进行滚动归档，压缩保存。


## 日志的性能

记录日志是软件开发必不可少的环节，必须考虑日志的性能带来的问题，举两个例子：

- 使用占位符而不是字符串连接

```
logger.debug("Entry number: " + i + " is " +  String.valueOf(entry[i])); 
logger.debug("Entry number: {} is {}", i, entry[i]);
```
上面两个语句输出的效果是一样的，但是性能却相差两个数量级，在关闭DEBUG日志时，它们的开销就不一样了，主要的影响在于字符串转换和字符串拼接上，无论是否生效，前者都会将变量转换为字符串并进行拼接，而后者则只会在需要时执行这些操作。

- 避免无用的日志
	- 无用的日志占用磁盘空间，增加系统IO，影响系统性能 
	

## 日志框架

列举一些 Java 程序里常用的日志框架

- Java Util Logger
	- Jdk 自带的 Logger， 从1.4版本开始就有了，使用起来非常方便，一般用于测试，不支持占位符
- Log4j
	- Log4j 应该是最常用的日志框架了，其高度可配置，在运行期使用外部的配置文件对其进行配置。它按照优先级别记录日志，并可将日志信息定向输出到各种介质，比如数据库、文件、控制台、Unix Syslog等。
	- loggers：负责采集日志信息。
	- appenders：负责将日志信息发布到不同地方。
	- layouts：负责以各种风格格式化日志信息。 	
- Slf4j
	- Slf4j是The Simple Logging Facade for Java的简称，是一个简单日志门面抽象框架，它本身只提供了日志Facade API和一个简单的日志类实现，一般常配合Log4j，LogBack，java.util.logging使用。Slf4j作为应用层的Log接入时，程序可以根据实际应用场景动态调整底层的日志实现框架(Log4j/LogBack/JdkLog…)。

- Logback
	- LogBack是Log4j的改良版本，比Log4j拥有更多的特性，同时也带来很大性能提升。LogBack官方建议配合Slf4j使用，这样可以灵活地替换底层日志框架。 
	- LogBack被分为3个组件，logback-core, logback-classic 和 logback-access。
	- logback-core提供了LogBack的核心功能，是另外两个组件的基础。
	- logback-classic则实现了Slf4j的API，所以当想配合Slf4j使用时，需要将logback-classic加入classpath。
	- logback-access是为了集成Servlet环境而准备的，可提供HTTP-access的日志接口。
- ...

## 最佳实践

- 定义记录日志的目标
	- 记录日志的意义何在，用于数据分析，还是同步数据，或者其他 
- 记录什么内容
	- 记录有用的日志内容，考虑提高日志可读性，提高系统性能 
- 使用日志框架
	- 日志框架更专业，功能全面，标准化，易配置
- 标准化日志格式
	- 统一日志格式 
- 提供上下文
	- 尽可能的提供完整信息，提高可读性，如果记录不完整，对于数据分析、排查错误起不到作用，记日志的意义就不大了 
- 使用日志标识，异常码
	- 使用异常码，例如`30001`代表用户模块，`30002`代表XX模块，这样在看到日志的时候就可以清楚快速的定位到对应的功能。 

## 参考文献

- [日志文件wiki](https://zh.wikipedia.org/wiki/日志文件)
- [30 best practices for logging at scale](https://www.loggly.com/blog/30-best-practices-logging-scale/)
- [How to Log a Log: Application Logging Best Practices](https://logz.io/blog/logging-best-practices/)
- [Java Best Practices for Smarter Application Logging & Exception Handling](https://stackify.com/java-logging-best-practices/)
- [10 Best Practices for Log Management and Analytics](https://dzone.com/articles/10-best-practices-for-log-management-and-analytics-1)
- [程序那些事：日志记录的作用和方法](https://www.infoq.cn/article/why-and-how-log)
- [日志的五个级别](https://www.infoq.cn/article/five-levels-of-logging)
- [你的Java日志，有没有用这些改进办法？](https://mp.weixin.qq.com/s?__biz=MjM5MDE0Mjc4MA==&mid=207451012&idx=1&sn=de9fba4eda0f221363b6d5ae54243416&scene=27#wechat_redirect)
- [Java 日志管理最佳实践](https://www.ibm.com/developerworks/cn/java/j-lo-practicelog/index.html)
- [日志最佳实践](http://blog.jobbole.com/56574/)
- [Ultimate log4j Tutorial for Java Logging – Best Practices, Resources and Tips](https://stackify.com/log4j-guide-dotnet-logging/)



