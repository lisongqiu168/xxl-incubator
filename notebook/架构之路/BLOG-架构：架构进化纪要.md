##### 架构1.0，131122，pook (neiqian，20台tomcat，2kw pv/d then)

基础架构：
- 1、集群：nginx + tomcat;
- 2、分布式缓存：memcached客户端分片集群；
- 3、分布式通讯：http + hex（封装sdk）;
- 5、消息队列：activemq(topic/queue)、memcacheq;
- 6、任务调度：quartz（xml配置方式）；

项目框架：
- 前端项目：struts2 + spring2 + ibatis
- 后台项目：struts2 + spring2 + hibernate3

数据库：oracle + 分库（同一个项目，连几个多个oracle实例，最多达8个）

项目划分：
- ***-net：官网
- ***-admin：游戏后台
- ***-lobby：内嵌，IOS、Android和PC端服务器，接口、H5页面或PC内嵌页面；
- ***-pay：官网充值；
- ***-center：充值中心；
...

---

##### 架构1.0，150620，项目管家网

服务器配置：
- 阿里云-ESC（杭州+4核+8G+1M带宽+CentOS6.5-64位）；
- SSH，开启22端口；
- jdk1.6-45（64bit-系统服务，编译安装）
- mysql5.5（64bit-系统服务，RPM安装），端口3306；
- memcached1.4.4-14（64bit-系统服务，yum安装），11211端口；
- activemq5.10.2（64bit-自启动配置，解压安装），61616端口；
- nginx1.8.0（64bit-系统服务，编译安装），80端口；
- tomcat6.0.44（64bit-解压安装，脚本手动启动）

```
/tomcat_servers（server.xml改端口，虚拟目录）
    |__pmp-admin01（8201）
    |__pmp-admin02（8202）
    |__pmp-net01（8211）
    |__pmp-net02（8212）
    |__pmp-net03（8213）
/tomcat_www
    |__pmp-admin
    |__pmp-net
```

---

##### 架构1.5，150304，lmm (~3w order/d then)

架构目标：高可用性（减少停工时间） + 数据安全

- 1、集群/负载均衡：
    - web集群：nginx + tomcat （绝大部分）
    - 服务集群：dubbo + zookeeper（小部分）
- 2、分布式-缓存：mecached+redis
- 3、分布式-通讯：解耦，高并发
    - web通讯：http + json
    - 服务通讯1：hessian（绝大部分）
    - 服务通讯2：dubbo + zookeeper（小部分）
- 4、消息队列-mq：activemq 主备
- 6、任务调度-job：quartz（XML配置方式），集群环境，通过差异化配置true/false，保证只有一台调用service时不会拒绝掉；
- 5、配置管理-conf：手动维护properties文件；

项目框架：springmvc3 + spring3 + mybatis + oracle

数据库：oracle 单库

SOA：
- ***_product 产品
- ***_front 详情
- ***_order 订单
- ***_workflow 工作流（订单由工作流驱动）

优化：jvm调优（sonar+代码重构+tdd+jvm调优+经验）

---

##### 架构2.0，150810，dp （动态pv 1亿/d then）
架构目标：高可用性（减少停工时间） + 数据安全

- 1、集群
    - web集群：nginx + tomcat
    - 服务集群：自研RPC（netty + zk）
- 2、分布式-缓存：定制redis
- 3、分布式-通讯
    - web通讯：http + json
    - 服务通讯2：自研RPC（netty + zk）
- 4、消息队列-mq：定制kafka
- 5、任务调度-job：自研
- 6、配置管理-conf：自研发（zk）

项目框架：struts2 + spring2 + ibatis + mysql

数据库：mysql 单库 + 读写分离

SOA：小团队作战
- ...-web
- ...-service (api/biz/service)
- ...-mapi
- ...

优化：jvm调优（sonar+代码重构+tdd+jvm调优+经验）

---

##### 架构2.0
130303初-160304高-170304初架：高可用+数据安全

六要素
- 1、集群（ha）：web/服务；高并发；高可用；负载均衡
    - nginx + tomcat
- 2、缓存（分布式、nosql）：session共享、db减压
    - redis
    - xxl-cache
- 3、通讯(服务)（分布式、rpc）：SOA，解耦；
    - restful：httpclient + json
    - dubbo
    - xxl-rpc
- 4、队列(消息)（分布式、mq）：异步通讯，解耦
    - activemq（主备、topic/queue）
    - kafka
    - xxl-mq
- 5、调度（分布式、定时任务、job）：quartz
    - xxl-job
    - quartz，xml配置方式
- 6、配置（分布式、conf）：统一配置管理、推送配置
    - 读取本地properties
    - xxl-conf

- 数据库：
    - mysql + 主备 + 读写分离 + 监控【xxl-db】
    - oracle：
    - mongodb：
- DB优化
    - a. 索引：见效最快，注意-避免sql不走索引情况
    - b. 读写分离：
    - c. 分库：集群环境下，单个库连接数不够（mysql>60、oracle>200），项目配置多个数据源实现；
    - d. 分表：数据水平分割，拆分多张表；
    - e. 定期数据迁移：大表，表中数据查询频率不高的情况下，可以定期将过期数据迁移至备份库中；
- 管理构建：gitlab + maven
- 框架：springmvc + mybtis
- 服务器拆分：
    - 文件服务器：快IO，CDN、七牛云存储
    - db服务器：快IO + 快CPU
    - 项目服务器：快CPU + 大内存
- SOA：
    - xxl-business-parent（api、biz、service）
    - xxl-net：官网pc/h5
    - xxl-e：商户后台
    - xxl-admin：业务后台
    - xxl-pay：支付中心
    - xxl-mapi：app服务器
    - xxl-wapi: web api服务器（前后端分离，html托管在cdn）
- 综合
    - 服务器减压：对象缓存》html整站静态化
    - 模块动态化：groovy
    - 代码优化01：代码重构+tdd
    - 代码优化02：sonar
    - jvm调优：jconsole
    - 工作流：activiti
    - 搜索：elasticsearch/lucene 
    - 大数据处理：日志/打点；[hadoop/kafka-zk-storm]