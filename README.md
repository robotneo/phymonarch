# idrac-monitor
💻📊🔥✅
监控戴尔服务器iDRAC

该项目主要采集戴尔服务器带外管理口 **`iDRAC`** 信息，实现对戴尔服务器各组件信息的监控和告警。

主流服务器品牌带外管理口基本上都支持 **`IPMI`** 、**`SNMP`** 、**`Redfish`** 协议，我这里主要以 **`SNMP`** 、**`Redfish`** 协议实现对物理机的监控告警。

## SNMP

以 **`Categraf`** 中 **`SNMP`** 插件和 **`SNMP Exporter`** 这两种作为采集器，物理机开启 **`SNMP`** 服务（出厂默认开启），以 **`SNMP v2c`** 版本为例，如果需要使用 **`SNMP v3`** 版本，可以自定义修改认证参数即可。

SNMP 协议实现方案链接：[使用教程](snmp/README.md)

**Grafana Dashboard ID:  [`21107`](https://grafana.com/grafana/dashboards/21107)**

## Redfish

以 **`Telegraf`** 中 **`Redfish`** 插件作为采集器，物理机开启 **`Redfish`** 服务（出厂默认开启），以 **`Redfish v1`** 版本为例，目前的最新版本就是 **`v1`** 版本。

基于 **`Redfish`** 协议开源的 **`idrac_exporter`** 也是一个比较好用的 **`pull`** 模式采集器，可结合 **`Prometheus`** 生态的时序库实现数据存储。

需要使用 **`idrac_exporter`** 请点击跳转到：[idrac_exporter](https://github.com/mrlhansen/idrac_exporter)

Redfish 协议实现方案链接：[使用教程](redfish/README.md)

**Grafana Dashboard ID: `待更新`**

## 更多信息

如果需要了解关于监控的更多信息，还请关注公众号：网络小斐，下面是公众号二维码。

![alt text](snmp/img/qrcode.jpg)