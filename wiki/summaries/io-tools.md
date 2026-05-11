---
title: IO 性能分析工具
created: 2026-05-11
updated: 2026-05-11
tags: [iostat, fio, IO, 性能分析, DFX]
sources: [DFX工具/==IO==]
---

# IO 性能分析工具

Linux IO 性能分析工具集，用于监控、测试和诊断磁盘性能问题。

## iostat 工具

### 基本用法
```bash
iostat -dmx 1 5 /dev/sda /dev/sdb
# -d: 只显示磁盘
# -m: 以 MB 为单位
# -x: 显示扩展统计
# 1: 每秒刷新
# 5: 显示 5 次
```

### 关键输出字段

| 字段 | 说明 |
|------|------|
| r/s | 每秒读 IO 请求数 |
| w/s | 每秒写 IO 请求数 |
| avgqu-sz | 平均请求队列长度 |
| await | 平均每次请求等待时间 (ms) |
| r_await/w_await | 读/写请求平均等待时间 |
| svctm | 平均每次请求服务时间 (ms) |
| %util | 设备利用率 (%) |

### 分析方法

| 场景 | 现象 | 分析 |
|------|------|------|
| 磁盘瓶颈 | util≈100%，svctm>5ms | 磁盘设备性能瓶颈，需硬件分析 |
| IO 请求积压 | r/s/w/s 高，svctm 小，avgqu-sz 大且 await>>svctm | 设备正常，业务 IO 请求过多 |
| 正常状态 | r/s/w/s 高，svctm 小，await≈svctm | 设备正常处理 |
| 性能对比 | svctm 越小越好（相同业务模型） | IO 密集型业务影响显著 |

## fio 工具

fio 是灵活的 IO 测试工具，支持多种测试模式。

### 随机读写测试
```bash
# 100% 随机读
fio -filename=/opt/testio -direct=1 -iodepth 1 -thread \
    -rw=randread -ioengine=psync -bs=8k -size=10G \
    -numjobs=50 -runtime=60 -group_reporting \
    -name=rand_100read_8k

# 100% 随机写
fio -filename=/opt/testio -direct=1 -iodepth 1 -thread \
    -rw=randwrite -ioengine=psync -bs=8k -size=10G \
    -numjobs=50 -runtime=60 -group_reporting \
    -name=rand_100write_8k
```

### 顺序读写测试
```bash
# 100% 顺序读
fio -rw=read -name=seq_100read_8k ...

# 100% 顺序写
fio -rw=write -name=seq_100write_8k ...
```

## dd 工具

简单读写测试：
```bash
# 读裸盘
dd if=/dev/sda of=/dev/null bs=5M count=10 iflag=direct

# 写文件（推荐）
dd if=/dev/zero of=/tmp/tmp.log bs=10M count=5 oflag=direct

# 写裸盘（谨慎）
dd if=/dev/zero of=/dev/sda bs=10M count=5 oflag=direct
```

## blktrace 工具

记录 IO 经历的各个步骤，分析是 IO Scheduler 还是硬件响应慢。

```bash
# 如果出现 Invalid argument 错误
echo $$ >> /sys/fs/cgroup/cpuset/cgroup.procs
```

参考: https://www.hikunpeng.com/document/detail/zh/perftuning/tuningtip/kunpengtuning_12_0036.html

## 存储 IO DFX

IO 流程分为 block 层和 SCSI 层：

| 层 | 日志开关 | 命令 |
|----|---------|------|
| Block 层 | block_dump | `echo 1/0 > /proc/sys/vm/block_dump` |
| SCSI 层 | SCSI_LOG_MLQUEUE | `scsi_logging_level -s --mlqueue=5/0` |

## 相关链接

- [[summaries/linux-io-mechanism]]
- [[summaries/linux-io-scheduler]]
- [[summaries/dfx-tools-overview]]