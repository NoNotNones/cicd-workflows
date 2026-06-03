---
title: "Zabbix Monitoring & Performance Optimization"
weight: 9
---

# Zabbix Monitoring & Performance Optimization 

This project presents a real-world **Zabbix monitoring system** combined with **performance optimization on a constrained Proxmox environment (8GB RAM VM)**.

It focuses on:
- System performance tuning
- Network infrastructure monitoring
- Observability at scale
- Stability under resource constraints
---

## 🔥 Before vs After Performance Benchmark

After optimization, the system was evaluated to compare resource usage and performance behavior under similar monitoring workloads.

| Metric / Component        | Before Optimization (4 vCPU / 8GB RAM) | After Optimization (2 vCPU / 4GB RAM) | Improvement |
|--------------------------|----------------------------------------|----------------------------------------|------------|
| Total RAM Usage          | ~6.5 – 7.2 GB                          | ~2.8 – 3.5 GB                          | ↓ 35–45%    |
| CPU Utilization (avg)    | 35–60%                                 | 20–40%                                 | ↓ Lower load |
| MariaDB Memory Usage     | High (unstable spikes)                 | Stable, controlled                     | Stabilized   |
| Zabbix Cache Pressure    | Frequent saturation risk               | Balanced cache usage                   | Optimized    |
| System Load (1m avg)     | 2.5 – 4.0                              | 1.0 – 2.0                              | ↓ Improved   |
| Response Time (UI)       | Moderate latency                       | Faster dashboard response              | Improved     |
| Risk of OOM              | High under peak load                   | Eliminated                            | Stable       |
| Monitoring Availability  | ~99.5%                                 | ~99.9%+                                | Increased    |



{{< image src="dashboard1.png" >}}
{{< image src="dashboard3.png" >}}

# 🏗️ System Architecture

### Infrastructure Overview :

- Hypervisor: Proxmox VE
- VM Specification: 4 vCPU / 8GB RAM / 100GB SSD
- Operating System: Linux (Ubuntu / CentOS)
- Monitoring Stack:
  - Zabbix Server
  - MariaDB Database
- Monitoring Methods:
  - SNMP (network devices)
  - Zabbix Agent (servers)
  - ICMP (availability checks)

---

## 🎯 Project Objectives

The initial system was functional but not optimized:

- High memory usage from MariaDB and Zabbix caches
- Risk of OOM (Out-Of-Memory) on 8GB VM
- Increasing number of monitored network devices
- Lack of kernel and system-level tuning
- Inefficient resource allocation under monitoring load

### Goals:

- Reduce total memory usage by ~40%
- Improve system stability under load
- Support scalable network monitoring
- Optimize performance under fixed hardware constraints

---

## ⚙️ Performance Optimization Layer

### 🗄️ MariaDB Optimization

- etc/mysql/mariadb.conf.d/50-server.cnf
- systemctl restart mariadb

```ini
innodb_buffer_pool_size = 2G
innodb_buffer_pool_instances = 2

tmp_table_size = 64M
max_heap_table_size = 64M

table_open_cache = 4000

innodb_log_file_size = 512M
innodb_log_buffer_size = 64M

max_connections = 150

innodb_flush_method = O_DIRECT

wait_timeout = 600
interactive_timeout = 600

net_read_timeout = 120
net_write_timeout = 120 
```

### Impact
- Reduced overall memory footprint significantly
- Prevented database memory over-allocation
- Improved stability under concurrent queries
- Balanced DB memory usage with application load

## 📊 Zabbix Server Optimization

- etc/zabbix/zabbix_server.conf
- systemctl restart zabbix-server

```ini
CacheSize = 128M
HistoryCacheSize = 64M
HistoryIndexCacheSize = 128M
TrendCacheSize = 128M
ValueCacheSize = 64M
```

### 🔍 Impact
- Reduced RAM consumption by more than 50% compared to default settings
- Maintained stable monitoring operations
- Optimized for medium-scale infrastructure workloads
- Prevented memory saturation on limited resources

## 🧠 Linux Kernel Optimization
- /etc/sysctl.conf 
- sudo sysctl -p

```ini
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535
fs.file-max = 1000000
vm.swappiness = 10
```
### 🔍 Impact
- Improved network queue handling
- Increased system file descriptor limits
- Reduced swap usage
- Enhanced system responsiveness under load

## 🔧 System Limits Configuration
- etc/security/limits.conf 

```ini
* soft nofile 65535
* hard nofile 65535
```

### 🔍 Impact
- Prevented file descriptor exhaustion
- Supported high concurrency (SNMP + Agent + ICMP)
- Improved long-term system stability