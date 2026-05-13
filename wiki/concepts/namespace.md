---
title: Namespace
created: 2026-05-13
updated: 2026-05-13
tags: [Linux, 容器, 隔离]
---

# Namespace

## 定义

Linux 内核隔离内核资源的方式，使进程拥有独立的系统资源视图。

## 七种类型

| 类型 | 隔离内容 | 容器应用 |
|------|----------|----------|
| PID | 进程ID | 容器有自己的init |
| Network | 网络栈 | 独立IP/端口 |
| Mount | 文件系统 | 独立挂载点 |
| UTS | 主机名 | 独立hostname |
| IPC | 进程通信 | 独立信号量 |
| User | 用户ID | 容器内root |
| Cgroup | Cgroup视图 | 新增 |

## 核心 API

```
clone()  → 创建新进程+新namespace
setns()  → 加入已有namespace
unshare() → 离开当前namespace
```

## 查看

```bash
ls -la /proc/$$/ns/
# 输出: user:[4026531837]
```

## 与容器关系

```
容器隔离 = Namespace(看见什么) + Cgroups(能用多少)
```

## 关联

- [[summaries/linux-namespace-cgroups]] - 完整介绍
- [[concepts/cgroups]] - 资源限制
- [[concepts/containerd]] - 容器运行时
- [[concepts/oci]] - 容器标准