---
title: Linux IO 调度算法
created: 2026-05-11
updated: 2026-05-13
tags: [IO, 调度算法, scheduler, Linux]
source_dir: Linux操作系统/Linux IO机制
source_files: [Linux IO调度算法.md]
---

# Linux IO 调度算法

Linux 内核 Block 层 IO 调度算法概览。

## IO 调度器类型

| 调度器 | 说明 | 适用场景 |
|--------|------|---------|
| noop | 无调度，直接提交 | SSD、虚拟化环境 |
| deadline | 保证请求截止时间 | 实时系统、数据库 |
| cfq | 完全公平队列 | 通用桌面系统 |
| mq-deadline | 多队列 deadline | 高性能设备 |
| bfq | Budget Fair Queue | 低延迟交互场景 |

## 调度算法选择

参考文章: https://cloud.tencent.com/developer/article/1615744

### SSD 场景
推荐 noop 或 mq-deadline，因为：
- SSD 无机械寻道延迟
- 调度开销可能成为瓶颈

### 虚拟化场景
推荐 noop：
- 宿主机已有调度
- 额外调度可能降低性能

### 数据库场景
推荐 deadline：
- 保证 IO 请求及时完成
- 防止某些请求被饿死

## 调度器切换

```bash
# 查看当前调度器
cat /sys/block/sda/queue/scheduler

# 切换调度器
echo deadline > /sys/block/sda/queue/scheduler
```

## 相关链接

- [[summaries/linux-io-mechanism]]
- [[summaries/io-tools]]
- [[summaries/dfx-tools-overview]]