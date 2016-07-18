
# Falcon


## 社区 | Open-Falcon  
  [http://book.open-falcon.org/zh/index.html](http://book.open-falcon.org/zh/index.html)


## 组件

- Agent  
    [http://book.open-falcon.org/zh/install_from_src/agent.html](http://book.open-falcon.org/zh/install_from_src/agent.html)
    - agent用于采集机器负载监控指标，比如cpu.idle、load.1min、disk.io.util等等，每隔60秒push给Transfer。agent与Transfer建立了长连接，数据发送速度比较快，agent提供了一个http接口/v1/push用于接收用户手工push的一些数据，然后通过长连接迅速转发给Transfer
- Transfer  
    [http://book.open-falcon.org/zh/install_from_src/transfer.html](http://book.open-falcon.org/zh/install_from_src/transfer.html)
    - transfer是数据转发服务。它接收agent上报的数据，然后按照哈希规则进行数据分片、并将分片后的数据分别push给graph&judge等组件。
- Graph  
    [http://book.open-falcon.org/zh/install_from_src/graph.html](http://book.open-falcon.org/zh/install_from_src/graph.html)
    - graph是存储绘图数据的组件。graph组件 接收transfer组件推送上来的监控数据，同时处理query组件的查询请求、返回绘图数据
- Query  
    [http://book.open-falcon.org/zh/install_from_src/query.html](http://book.open-falcon.org/zh/install_from_src/query.html)
    - query组件，提供统一的绘图数据查询入口。query组件接收查询请求，根据一致性哈希算法去相应的graph实例查询不同metric的数据，然后汇总拿到的数据，最后统一返回给用户
- DashBoard  
    [http://book.open-falcon.org/zh/install_from_src/dashboard.html](http://book.open-falcon.org/zh/install_from_src/dashboard.html)
    - dashboard是面向用户的查询界面。在这里，用户可以看到push到graph中的所有数据，并查看其趋势图
- Sender  
    [http://book.open-falcon.org/zh/install_from_src/sender.html](http://book.open-falcon.org/zh/install_from_src/sender.html)
    - Sender这个模块专门用于调用各公司提供的邮件、短信发送接口
- Web前端 Fe  
    [http://book.open-falcon.org/zh/install_from_src/fe.html](http://book.open-falcon.org/zh/install_from_src/fe.html)
    - Go版本的UIC，也是一个统一的web入口，因为监控组件众多，记忆ip、port去访问还是比较麻烦。fe像是一个监控的hao123。  
      可以在Fe中维护个人联系信息，维护人和组的对应关系。还可以通过配置文件中配置的shortcut在页面上产生多个快捷方式链接
- Portal  
    [http://book.open-falcon.org/zh/install_from_src/portal.html](http://book.open-falcon.org/zh/install_from_src/portal.html)
    - 用来配置报警策略
- HBS(Heartbeat Server)  
    [http://book.open-falcon.org/zh/install_from_src/hbs.html](http://book.open-falcon.org/zh/install_from_src/hbs.html)
    - 心跳服务器，公司所有agent都会连到HBS，每分钟发一次心跳请求
- Judge  
    [http://book.open-falcon.org/zh/install_from_src/judge.html](http://book.open-falcon.org/zh/install_from_src/judge.html)
    - Judge用于告警判断，agent将数据push给Transfer，Transfer不但会转发给Graph组件来绘图，还会转发给Judge用于判断是否触发告警
- Links  
    [http://book.open-falcon.org/zh/install_from_src/links.html](http://book.open-falcon.org/zh/install_from_src/links.html)
    - Links是为报警合并功能写的组件。如果你不想使用报警合并功能，这个组件是无需安装的
- Alarm  
    [http://book.open-falcon.org/zh/install_from_src/alarm.html](http://book.open-falcon.org/zh/install_from_src/alarm.html)
    - alarm模块是处理报警event的，judge产生的报警event写入redis，alarm从redis读取处理
- Task  
    [http://book.open-falcon.org/zh/install_from_src/task.html](http://book.open-falcon.org/zh/install_from_src/task.html)
    - task是监控系统一个必要的辅助模块。定时任务，实现了如下几个功能：  
      index更新。包括图表索引的全量更新 和 垃圾索引清理。  
      falcon服务组件的自身状态数据采集。定时任务了采集了transfer、graph、task这三个服务的内部状态数据。  
      falcon自检控任务。
- Nodata  
    [http://book.open-falcon.org/zh/install_from_src/nodata.html](http://book.open-falcon.org/zh/install_from_src/nodata.html)
    - nodata用于检测监控数据的上报异常。nodata和实时报警judge模块协同工作，过程为: 配置了nodata的采集项超时未上报数据，nodata生成一条默认的模拟数据；用户配置相应的报警策略，收到mock数据就产生报警。采集项上报异常检测，作为judge模块的一个必要补充，能够使judge的实时报警功能更加可靠、完善
- Aggregator  
    [http://book.open-falcon.org/zh/install_from_src/aggregator.html](http://book.open-falcon.org/zh/install_from_src/aggregator.html)
    - 集群聚合模块。聚合某集群下的所有机器的某个指标的值，提供一种集群视角的监控体验

## 数据链路

- ![IMG](http://o8qzh1ara.bkt.clouddn.com/Falcon-Data.jpg)

## Falcon-yiji

- 自定义Agent监控  
  Yiji-Falcon-Agent
    - Falcon Agent的集成
        - 因所有的监控数据都必须要上报到Falcon Agent，所以为了部署方便和管理，此Agent集成了Falcon Agent，启动时会同时启动自带的Falcon Agent，关闭时也会同时关闭Falcon Agent。
    - Agent命令
        - 启动：./bin/agent.sh start
            - 启动日志查看
                - 可通过  
                  tail -f conf/console.log  
                  观察agent的运行情况
        - 关闭：./bin/agent.sh stop
        - 状态：./bin/agent.sh status
    - Agent日志
        - Agent运行中的日志分为四种：  
          1、console（debug）  
          2、info  
          3、warn  
          4、error  
          每种日志均自动控制日志大小，每到5MB就自动进行日志分割，最多有10个同类文件。既所有的日志文件，最多只会达到200MB，无需担心日志文件过于庞大。
    - endpoint命名
        - 见agent.properties的endpoint配置
    - metrics命名
        - 若属性文件配置了alias，则为alias
    - tag命名
        - tag来区分服务  
          例如：  
          **service={value}** - （內建）服务产品名，如tomcat  
          **service.type={value}** - （內建）监控服务类型，如jmx,database  
          **agentSignName={value}** - （內建）agent提供的标识字符串，如 allUnVariability（代表该服务agent启动时就不存在）**，**标识服务的占用端口号，服务名等，  
          **metrics.type={value}** - （內建）监控值类型，如availability，jmxObjectConf，jmxObjectInBuild，httpUrlConf，sqlConf，sqlInBuild，snmpConnomInBuild，snmpPluginInBuild
        - 可用性(availability)会自动打上标签：  
          **metrics.type=availability,service={value}**
    - agent-plugin
        - Agent所有的监控服务都是插件式开发集成
        - 插件开发
            - JDBC的监控服务，实现JDBCPlugin接口  
              JMX的监控服务，实现JMXPlugin接口  
              SNMP的监控服务，实现SNMPV3Plugin接口
    - 监控服务
        - JMX监控
            - Agent JMX连接获取方式
                - 与jconsole本地连接获取的相同方式，无需配置jmx连接，只需要指定对应的jmx的serverName即可，支持单机多实例的监控
            - 特殊的metrics说明
                - 若agent在启动时，需要进行监控的服务（对应的work配置为true的）未启动，则将会上报一个名为**allUnVariability**的metrics监控指标，值为0。tag中有metrics的详情（参考tag命名），代表为该服务全部不可用
            - JMX监控属性组成
                - Agent内置的JMX监控属性
                    - HeapMemoryUsedRatio - 堆内存使用比例
                    - HeapMemoryCommitted - 堆内存已提交的大小
                    - NonHeapMemoryCommitted - 非堆内存已提交的大小
                    - HeapMemoryFree - 堆内存空闲空间大小
                    - HeapMemoryMax - 堆内存最大的空间大小
                    - HeapMemoryUsed - 堆内存已使用的空间大小
                    - NonHeapMemoryUsed - 非堆内存已使用的空间大小
                - JMX 公共的监控属性自定义配置
                    - 定义于conf/jmx/common.properties文件
                - 自定义的监控属性
                    - 每个插件自定义的属于自身的监控属性
            - 目前支持的监控组件
                - zookeeper
                - tomcat
                - elasticSearch
                - logstash
                - yijiBoot应用
        - JDBC监控
            - 目前支持的监控组件
                - Oracle
        - SNMP监控
            - 公共的metrics列表
                - IfHCInOctets
                - IfHCOutOctets
                - IfHCInUcastPkts
                - IfHCOutUcastPkts
                - IfHCInBroadcastPkts
                - IfHCOutBroadcastPkts
                - IfHCInMulticastPkts
                - IfHCOutMulticastPkts
                - IfOperStatus(接口状态，1 up, 2 down, 3 testing, 4 unknown, 5 dormant, 6 notPresent, 7 lowerLayerDown)
                - Ping延时（正常返回延时，超时返回 -1，可以用于存活告警）
            - 交换机（SNMP V3）
                - 说明
                    - 监控的设备采集信息和采集逻辑主要参考了Falcon社区的swcollector项目，因swcollector不支持SNMP V3协议。  
                        [https://github.com/gaochao1/swcollector](https://github.com/gaochao1/swcollector)
                - 采集的私有metric列表
                    - 公共的metrics数据  
                      ****
                    - CPU利用率
                    - 内存利用率
                - 内存和CPU的目前测试的支持设备
                    - Cisco IOS(Version 12)
                    - Cisco NX-OS(Version 6)
                    - Cisco IOS XR(Version 5)
                    - Cisco IOS XE(Version 15)
                    - Cisco ASA (Version 9)
                    - Ruijie 10G Routing Switch
                    - Huawei VRP(Version 8)
                    - Huawei VRP(Version 5.20)
                    - Huawei VRP(Version 5.120)
                    - Huawei VRP(Version 5.130)
                    - Huawei VRP(Version 5.70)
                    - Juniper JUNOS(Version 10)
                    - H3C(Version 5)
                    - H3C(Version 5.20)
                    - H3C(Version 7)
        - 自动发现服务监控说明
            - zookeeper
                - 无需配置，可自动发现
            - tomcat
                - 无需配置，可自动发现
            - elasticSearch
                - 无需配置，可自动发现
            - logstash
                - 无需配置，可自动发现
            - yijiBoot应用
                - 需要配置conf/plugin/yijiBootPlugin.properties配置文件的jmxServerName，然后Agent会自动发现已配置的yijiBoot应用
            - Oracle
                - 需要配置conf/authorization.properties配置中的Oracle连接信息，然后会根据配置的连接信息，进行自动发现Oracle应用，并监控
            - SNMP V3 交换机
                - 需要配置conf/authorization.properties配置中的交换机的SNMP V3的连接信息，然后会根据配置的连接信息，进行自动发现交换机并监控。
    - 监控配置
        - Falcon Agent配置
            - conf/falcon/agent.cfg.json
        - Agent配置
            - conf/agent.properties
        - 授权配置
            - conf/authorization.properties
        - 插件配置
            - conf/plugin目录下
    - Q & A
        - 为什么有时候JMX应用启动了，但是Agent总是报无法获取JMX连接
            - 如果JMX应用启动时用的用户（如root或非root）和Agent启动时用的用户身份不一样，可能会出现无法获取JMX连接。目前发现，在ubuntu和Mac系统上，用root身份启动Agent，也出现无法获取用非root身份启动的elasticSearch应用的JMX连接。  
                
              **解决方案**：用与应用相同的用户身份再启动一个新的Agent实例，然后进行对该应用的单独监控。需要注意重新制定Agent的监听端口和应用监控的work配置
- 监控自动化
    - 监控方案
        - **对于****Falcon****自带的****Agent****监控的值，如****cpu.idle****等，没有任何****tag****信息，用****hostGroups + Templates****的方式进行监控管理**  
          ****
            - 快速入门 | Open-Falcon  
                [http://book.open-falcon.org/zh/usage/getting-started.html](http://book.open-falcon.org/zh/usage/getting-started.html)
            - Tag和HostGroup | Open-Falcon  
                [http://book.open-falcon.org/zh/philosophy/tags-and-hostgroup.html](http://book.open-falcon.org/zh/philosophy/tags-and-hostgroup.html)
        - **对于自己开发的****Agent****监控的值，可以定义****tag****规则进行上报，用****Expressions****的方式进行监控管理**
    - 自动化发布系统部署应用时自动监控
        - 1、自动部署Agent
            - 部署Agent
            - 修改Agent配置
                - tomcat应用：监控tomcat，可自动发现  
                  yiji-boot应用：配置启动类main方法
            - 启动Agent
        - 2、自动配置报警
            - 自动添加应用报警对象（用户、通讯组）
            - 自动配置Expression监控表达式，匹配对应的通讯组
    - 应用自动化部署，监控自动进行监控停止和恢复
        - 操作：  
          应用停止时，暂停对应的Expression监控表达式  
          应用启动后，开启对应的Expression监控表达式  
          应用删除后，删除对应的Expression监控表达式，用户，通讯组
            - 直接操作数据库，hbs每分钟从DB中load各种数据，处理后放到内存里，静待agent、judge的请求
