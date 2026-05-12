---
title: Linux 调度策略
created: 2026-05-12
updated: 2026-05-12
tags: [Linux, 调度策略, SCHED_OTHER, SCHED_FIFO, SCHED_RR]
---

# Linux 调度策略

Linux内核支持三种主要调度策略。

## 策略对比

| 策略 | 类型 | 时间片 | 适用场景 |
|------|------|--------|----------|
| SCHED_OTHER | 分时 | 动态 | 普通进程 |
| SCHED_FIFO | 实时 | 无 | 高优先级实时任务 |
| SCHED_RR | 实时 | 固定 | 需要轮转的实时任务 |

## SCHED_OTHER

- 默认分时调度策略
- 基于nice值和counter值决定权值
- nice越小、counter越大 → 被调度概率越大

## SCHED_FIFO

- 实时调度，先到先服务
- 无时间片限制
- 高优先级一直运行直到主动放弃或被更高优先级抢占

## SCHED_RR

- 实时调度，时间片轮转
- 同优先级进程轮流执行
- 时间片用完重新入队

## 优先级范围

| 类型 | 范围 |
|------|------|
| 实时优先级 | 0-99（越高越优先） |
| 普通优先级 | 100-139（对应nice -20到19） |

## 相关链接

- [[summaries/linux-scheduler]]
- [[concepts/cfs-scheduler]]