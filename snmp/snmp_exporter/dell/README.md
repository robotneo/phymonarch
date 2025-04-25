## 戴尔服务器带外管理采集

**`SNMP Exporter`** 采集戴尔 **`iDRAC`** 的指标 

### 如何使用

- 安装并部署 **`SNMP Exporter`** 服务，并设置 **`systemd`** 服务管理
- 在 **`vmagent`** 或者 **`Prometheus`** 下配置任务抓取


---
安装部署 **`SNMP Exporter`** 服务

```bash
# 下载 SNMP Exporter 二进制安装包
wget https://github.com/prometheus/snmp_exporter/releases/download/v0.29.0/snmp_exporter-0.29.0.linux-amd64.tar.gz
tar -zxvf snmp_exporter-0.29.0.linux-amd64.tar.gz -C /opt/snmp_exporter
mkdir -pv /opt/snmp_exporter/conf
```

将 **`snmp_exporter`** 的作为服务运行，新建服务启动文件 **`/etc/systemd/system/snmp_exporter.service`**

```bash
[Unit]
Description=snmp_exporter
After=network.target

[Service]
ExecStart=/opt/snmp_exporter/snmp_exporter --config.file=/opt/snmp_exporter/conf/snmp_*.yml --snmp.module-concurrency=3
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

把采集配置文件 **`snmp_idrac.yml`** 放到 **`/opt/snmp_exporter/conf/`** 目录下

---
配置抓取任务

```yaml
scrape_configs:
  - job_name: "dell_idrac"
    scrape_interval: 60s
    scrape_timeout: 50s
    file_sd_configs:
      - files:
        - /etc/victoriametrics/vmagent/dell_idrac.yml
    metrics_path: /snmp
    relabel_configs:
    - source_labels: ["__address__"]
      target_label: __param_target
    - source_labels: ["__param_target"]
      target_label: instance
    - target_label: __address__
      replacement: 172.17.40.142:9116
    - source_labels: ["module"]
      target_label: __param_module
    - source_labels: ["auth"]
      target_label: __param_auth
```

上面是通过文件服务发现的方式发行监控对象，故我们需要新建 **`dell_idrac.yml`** 文件，并放到对应的目录下，我这里是放在 **`/etc/victoriametrics/vmagent`** 目录下：

```yaml
- labels:
    module: idrac_system,idrac_hardware,idrac_frupci,idrac_storage,idrac_power
    auth: idrac_auth    # snmp_idrac.yml 文件中定义的认证模块，请自行修改认证信息
    brand: Dell         # 品牌标签
    role: server        # 监控对象角色
    region: hangzhou    # 对象所在地域
  targets:
    - 172.16.10.1
    - 172.16.10.2
    - 172.16.10.3
    - 172.16.10.4
    - 172.16.10.5       # 更多 target 可继续添加
```

到此就完成戴尔 **`iDrac`** 对象的监控采集。