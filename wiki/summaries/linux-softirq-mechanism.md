---
title: Linux 软中断机制详解
created: 2026-05-16
updated: 2026-05-16
tags: [Linux, softirq, ksoftirqd, 中断, 内核]
source_dir: Self learn
source_files: [中断-ArthurChiao-IRQ softirq原理.md, 中断-博客园-ksoftirqd详解.md, 中断-腾讯云-软中断详解.md]
credibility: low
---

# Linux 软中断机制详解

软中断（softirq）是 Linux 内核处理硬中断下半部的核心机制，用于延迟处理中断上半部未完成的工作。

---

## 一、中断上下半部分割

### 为什么需要分割？

中断处理存在内在矛盾：
1. **执行要足够快**：否则会导致事件和数据丢失
2. **逻辑可能很复杂**：例如网卡收包需要协议栈处理

### 分割机制

| 部分 | 名称 | 执行环境 | 特点 | 职责 |
|------|------|----------|------|------|
| 上半部 | 硬中断（IRQ） | 中断上下文，关中断 | 快速执行 | 处理硬件紧密相关的任务 |
| 下半部 | 软中断（softirq） | 软中断上下文，开中断 | 延迟执行 | 处理上半部未完成的工作 |

### 外卖类比

| 场景 | 对应中断 |
|------|----------|
| 配送员打电话通知送达 | 硬中断触发 |
| 接电话说"知道了" | 上半部快速响应 |
| 挂电话后去取外卖 | 下半部延迟处理 |
| 电话占线导致第二份外卖丢失 | 关中断导致中断丢失 |

---

## 二、软中断子系统架构

### ksoftirqd 内核线程

每个 CPU 对应一个 ksoftirqd 内核线程：

```bash
$ ps aux | grep ksoftirqd
root  3  [ksoftirqd/0]  # CPU 0
root  9  [ksoftirqd/1]  # CPU 1
root 12  [ksoftirqd/2]  # CPU 2
root 15  [ksoftirqd/3]  # CPU 3
```

**命名规则**：`ksoftirqd/CPU编号`

### 软中断向量表

内核编译时确定的静态数组：

```c
// kernel/softirq.c
static struct softirq_action softirq_vec[NR_SOFTIRQS];

void open_softirq(int nr, void (*action)(struct softirq_action *)) {
    softirq_vec[nr].action = action;  // 注册处理函数
}
```

### 软中断 10 种类型

| 类型 | 名称 | 用途 |
|------|------|------|
| HI_SOFTIRQ | 高优先级tasklet | 优先执行 |
| TIMER_SOFTIRQ | 定时器 | 时间回调 |
| NET_TX_SOFTIRQ | 网络发送 | 协议栈TX |
| NET_RX_SOFTIRQ | 网络接收 | 协议栈RX（高频） |
| BLOCK_SOFTIRQ | 块设备IO | 磁盘读写 |
| IRQ_POLL_SOFTIRQ | IRQ轮询 | |
| TASKLET_SOFTIRQ | 普通tasklet | 延迟任务 |
| SCHED_SOFTIRQ | 调度 | 负载均衡 |
| HRTIMER_SOFTIRQ | 高精度定时器 | |
| RCU_SOFTIRQ | RCU锁 | 读拷贝更新 |

---

## 三、__do_softirq 主处理逻辑

### 核心流程

```
pending = local_softirq_pending()  // 获取待处理软中断位图
    ↓
restart:
while ((softirq_bit = ffs(pending)))  // 找到第一个置位
    h += softirq_bit - 1              // 定位处理函数
    h->action(h)                      // 执行软中断处理
    pending >>= softirq_bit           // 位图右移
    ↓
检查条件：time_before(jiffies, end) && !need_resched() && --max_restart
    ↓
满足 → goto restart                   // 继续循环
不满足 → wakeup_softirqd()            // 唤醒ksoftirqd线程
```

### 防止软中断占用过多 CPU

```c
unsigned long end = jiffies + MAX_SOFTIRQ_TIME;  // 最大执行时间
int max_restart = MAX_SOFTIRQ_RESTART;           // 最大循环次数（10次）

if (pending) {
    if (time_before(jiffies, end) && !need_resched() && --max_restart)
        goto restart;
    wakeup_softirqd();  // 超时或次数耗尽，唤醒线程
}
```

---

## 四、ksoftirqd 触发机制

### 触发条件

| 条件 | 说明 |
|------|------|
| `MAX_SOFTIRQ_TIME` | 软中断处理超过 2 jiffies（约10ms） |
| `max_restart` | 循环次数超过 10 次 |
| `need_resched()` | 有进程需要调度 |

### 唤醒路径

```c
// 硬中断退出时检查
if (!in_interrupt() && local_softirq_pending())
    invoke_softirq();  // 尝试立即执行

// 条件不满足时唤醒线程
wakeup_softirqd():
    wake_up_process(ksoftirqd);  // 唤醒ksoftirqd线程
```

---

## 五、三种推迟执行方式对比

| 方式 | 执行上下文 | 特点 | 适用场景 |
|------|-----------|------|----------|
| **softirq** | 软中断上下文 | 静态、编译时确定、并发执行 | 网络收发、定时器、RCU |
| **tasklet** | 软中断上下文 | 动态、基于softirq、串行执行 | 一般延迟任务 |
| **workqueue** | 内核进程上下文 | 可睡眠、可调度、优先级低 | 复杂耗时任务 |

### 使用选择

- **需要并发** → softirq
- **需要动态创建** → tasklet 或 workqueue
- **需要睡眠/阻塞** → workqueue

---

## 六、监测与调试

### 查看软中断统计

```bash
$ cat /proc/softirqs
CPU0       CPU1
HI:        0         0
TIMER:     811613    1972736
NET_TX:    49        7
NET_RX:    1136736   1506885  # 网络接收高频
BLOCK:     0         0
TASKLET:   304787    3691
SCHED:     689718    1897539
HRTIMER:   0         0
RCU:       1330771   1354737
```

**关注点**：
1. 同种软中断在不同 CPU 的分布是否均衡
2. NET_RX/NET_TX 网络中断数量（高频场景）
3. TASKLET 单 CPU 分布不均衡问题

### 查看 CPU 软中断开销

```bash
$ top -n1 | head -n3
%Cpu(s): 13.9 us, 3.2 sy, 0.0 ni, 82.7 id, 0.0 wa, 0.0 hi, 0.1 si
# si = softirq 软中断占比
```

### 网卡收包示例流程

```
网卡收到数据包
    ↓
硬中断（IRQ）触发 ───── 上半部
    ↓
ISR读取数据到内存
更新硬件寄存器状态
    ↓
raise_softirq(NET_RX_SOFTIRQ)
    ↓
软中断触发 ───── 下半部
    ↓
__do_softirq() 或 ksoftirqd
    ↓
net_rx_action() → poll() → 协议栈处理
```

---

## 七、常见问题与解决

### 软中断 CPU 使用率过高

**现象**：top 显示 `si` 占比高

**原因**：
- 网络包过多（NET_RX_SOFTIRQ）
- 单 RX queue 导致单 CPU 繁忙

**解决**：
- RPS（Receive Packet Steering）重新分发包
- 多队列网卡配置

### 中断丢失

**原因**：硬中断处理时关中断

**解决**：上下半部分割，上半部快速返回

---

## 相关链接

- [[summaries/linux-irq-interrupt]] - IRQ 硬中断基础
- [[summaries/linux-scheduler]] - 进程调度与中断关联
- [[summaries/interrupt-monitoring]] - 中断监控工具
- [[concepts/linux-rcu]] - RCU_SOFTIRQ 锁机制
- [[summaries/linux-network-stack]] - NET_RX/NET_TX 网络中断

---

*来源：Self learn 目录，可信度：低。建议阅读原文验证关键内容。*