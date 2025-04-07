## 浪潮服务器带外管理采集

**`SNMP Exporter`** 采集浪潮 **`BMC`** 的指标

基于：浪潮 5280M6 各版本 OID 获取异常统计

```
总功率         V4正常   V7异常（会存在无法获取值）
风扇           V4异常   V7正常
物理磁盘        V4异常   V7正常
逻辑磁盘        V4异常   V7正常
RAID          V4异常   V7正常
PCIE(总览)     V4正常   V7异常（获取值只能一半就timeout）
网卡           V4(缺少网卡设备名称和MAC地址)     V7正常
```

2、BMC V4 采集配置
```
- job_name: "inspur_bmc_v4"
  scrape_interval: 1m
  scrape_timeout: 30s
  file_sd_configs:
    - files:
      - /opt/app/vmagent/inspur_bmc.yml
      # refresh_interval: 2m  # vmagent 不支持这个参数
  metrics_path: /snmp
  relabel_configs:
  - source_labels: ["__address__"]
    target_label: __param_target
  - source_labels: ["__param_target"]
    target_label: instance
  - source_labels: ["__param_target"]
    target_label: device_ip
  - target_label: __address__
    replacement: x.x.x.x:9116
  - source_labels: ["module"]
    target_label: __param_module
  - source_labels: ["auth"]
    target_label: __param_auth
  metric_relabel_configs:
  - source_labels: [__name__]
    regex: '(.*)'
    replacement: "snmp_$1"
    target_label: __name__
  - action: labeldrop
    regex: (auth|module)
```

### inspur_bmc.yml

```
# V4版本
- labels:
    # BMC V4
    module: inspur_general,inspur_temperature,inspur_system,inspur_power,inspur_pcie
    # 认证模块名称，如果团体名或认证信息不对，需要修改认证模块中的验证信息
    auth: auth_inspur_bmc
    brand: inspur
    role: bmc
    region: hangzhou
  targets:
    - x.x.x.x
```

```
- labels:
    # BMC V7
    module: inspur_general,inspur_temperature,inspur_system,inspur_power,inspur_fan,inspur_disk
    # 认证模块名称，如果团体名或认证信息不对，需要修改认证模块中的验证信息
    auth: auth_inspur_bmc
    brand: inspur
    role: bmc
    region: hangzhou
  targets:
    - x.x.x.x
```