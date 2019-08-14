# 开发者对sonic概述

参考[sonic官方库 Developer's overview of sonic]([https://github.com/Azure/SONiC/blob/master/doc/Developer's%20Overview%20of%20SONiC%20-%20LNKD.pdf](https://github.com/Azure/SONiC/blob/master/doc/Developer's Overview of SONiC - LNKD.pdf))

## 如何成为开发者

1. ) https://github.com/Azure/SONiC/wiki
2. uesr guide
3. Developer`s overview Session(s).
   ** New Sonic Wiki will have link to User Guide & 
   OCP.
   ** https://sonicswitch.slack.com/messages (Join 
   Slack) 

## sonic的整体架构

- ### 基本模块

![基本功能模块](https://img-blog.csdn.net/20180803132126345?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdXN5MTEyOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



- ### 具体模块间交互

1. redis-sever,不同的数据库，键值的设置，和redis交互的基本library

2. 容器的相互作用

3. 同步进程

   fpm lldp team port 以及neigh同步

4. orchagent【swss docker】
5. 使用sonic基本镜像或者在一个新的Docker中编译文件总概性构造一个新的特性

![1565603764155](/home/ben/.config/Typora/typora-user-images/1565603764155.png)

从这个图分析，似乎swss container以及database container这两个容器是核心组件，上面的各个功能模块以及下层内核等都需要和其进行交互。

## 数据库简介

以下表格内容以及定义在[sonic-swss-common/common/schema.h](https://github.com/Azure/sonic-swss-common/blob/master/common/schema.h)中有引用

![database](/home/ben/Documents/SONiC-NOTE/picture/database.png)

| DB name     | DB No. | Description                                          | Additional Information                                       |
| :---------- | ------ | ---------------------------------------------------- | :----------------------------------------------------------- |
| APP_DB      | 0      | ARP/NDP条目<br />BGP路由<br />LLDP表项<br />下一条等 | route_table<br />intf_table<br />neigh_table<br />vlan_member_table<br />port_table<br />copp_table<br />lldp_entry_table<br />等都属于这个表项的内容 |
| ASIC_DB     | 1      | 运行芯片的配置以及芯片状态数据                       | asic_state:br />sai_object_type_route_entry<br />asic_state:sai_object_type_next_hop_group+member<br />asic_state:sai_object_type_neighbor_entry |
| COUNTERS_DB | 2      | 端口计数器数据；lag;队列；acl                        | 计数器：<br />CRM：ACL_STATS:INGRESS:LAG                     |
| LOGLEVEL_DB | 3      | SONIC子系统日志级别控制                              | swssloglevel -p<br/>buffermgrd NOTICE<br/>fpmsyncd NOTICE<br/>intfmgrd NOTICE<br/>intfsyncd NOTICE<br/>neighsyncd NOTICE<br/>orchagent NOTICE<br/>portsyncd NOTICE<br/>syncd NOTICE<br/>teamsyncd NOTICE<br/>vlanmgrd NOTICE |
| CONFIG_DB   | 4      | sonic 配置数据库                                     | ==/etc/sonic/config_db.json==                                |
| STATE_DB    | 6      | 配置数据库（config_db）中对象的操作状态              | PORT_TABLE\|Ethernet106<br />VLAN_TABLE\|VLAN567<br />VLAN_TABLE\|VLAN234 |



## redis 数据库：键值&python数据库

Python库如下：

端口Python文件[interface.py](https://github.com/Azure/sonic-py-swsssdk/blob/master/src/swsssdk/interface.py)

数据库配置Python文件[configdb](https://github.com/Azure/sonic-py-swsssdk/blob/master/src/swsssdk/configdb.py)

数据库通信Python文件[dbconnector](https://github.com/Azure/sonic-py-swsssdk/blob/master/src/swsssdk/dbconnector.py)

