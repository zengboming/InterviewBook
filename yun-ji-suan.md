#### 启动虚机流程

1.客户端使用自己的用户名密码请求认证。

2.keystone通过查询在keystone的数据库user表中保存了user的相关信息，包括password加密后的hash值，并返回一个token\_id（令牌），和serviceCatalog\(一些服务的endpoint地址，cinder、glance-api后面下载镜像和创建块存储时会用到\)。

3.客户端带上keystone返回的token\_id和创建虚机的相关参数，Post请求nova-api创建虚拟机

4.nova-api接收到请求后，首先使用请求携带的token\_id来访问该api，以验证请求是否有效。

5.keystone验证通过后返回更新后的认证信息。

6.nova api检查创建虚拟机参数是否有效与合法。检查虚拟机name是否符合命名规范，flavor\_id是否在数据库中存在，image\_uuid是否是正确的uuid格式检查instance、vcpu、ram的数量是否超过配额。

7.当且仅当所有传参都有效合法时，更新nova数据库，新建一条instance记录，vm\_states设为BUILDING，task\_state设为SCHEDULING.

8.nova api 远程调用传递请求、参数给nova scheduler，把消息“请给我创建一台虚拟机”丢到消息队列，然后定期查询虚机的状态。

9.nova scheduler从queue中获取到这条消息

10.nova scheduler访问nova 数据库，通过调度算法，过滤出一些合适的计算节点，然后进行排序。

11.更新虚机节点信息，返回一个最优节点id给nova scheduler。

12.nova scheduler选定host之后，通过rpc调用nova-compute服务，把“创建虚机请求”消息丢个mq。

13.nova compute收到创建虚拟机请求的消息

\#nova-compute有个定时任务，定期从数据库中查找到运行在该节点上的所有虚拟机信息，统计得到空闲内存大小和空闲磁盘大小。然后更新数据库compute\_node信息，以保证调度的准确性。

14.nova compute通过rpc查询nova数据库中虚机的信息例如主机模板和id

15.nova conductor从消息队列中拿到请求查询数据库

16.nova conductor查询nova数据库

17.数据库返回虚机信息

18.nova compute从消息队列中获取信息。

19.nova compute 请求glance 的rest api，下载所需要的镜像

20.glance api 也会去验证请求的token的有效性。

21.glance api 返回镜像信息给nova-compute。

22.同理，nova compute请求neutron api配置网络，例如获取虚机ip地址

23.验证token的有效性

24.neutron返回网络信息

25-27 同glance、neutron验证token返回块设备信息

28.据上面配置的虚拟机信息，生成xml，写入libvirt,xml文件，然后调用libvirt driver去使用libvirt.xml文件启动虚拟机。

![](/assets/importVM.png)

组件之间用rest API通信，组件内用RPC通信

