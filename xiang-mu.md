# 实习

#### 华为

在实习期间负责制作RPM包和存储网关Windows磁盘容灾原型，存储网关是用来在混合云下容灾备份的，具有Backup、Replication和Snapshot功能。I/O截获是存储网关的“入口”，通过磁盘卷过滤驱动在Windows内核态截获I/O，该项目涉及Windows内核编程，WinDbg双机调试等技术。存储网关项目是一个开源项目，可对接Openstack的Karbor模块，目前还处于预研阶段。

#### 数梦

1. EFK日志监控报警：使用Fluentd收集集群各个模块，如Nova、Neutron、Keystone等模块的日志，使用Elasticsearch实现日志的存储和日志的精确查询和模糊匹配，并用ElastAlert实现日志报警。该系统实现了Log的可视化查询，帮助问题的快速定位。
2. Openstack资源监控：使用Zabbix来监控云下资源，Ceilometer来监控云上资源。云下资源主要是物理机的资源，云上资源主要是虚机的资源。通过设置监控项，然后定时获取监控数据，并显示在页面上来实现可视化运维。



