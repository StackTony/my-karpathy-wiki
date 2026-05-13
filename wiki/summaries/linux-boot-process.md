---
title: Linux 启动详细过程
created: 2026-05-13
updated: 2026-05-13
tags: [Linux, 启动, BIOS, MBR, GRUB, init]
source_dir: Linux操作系统/Linux系统启动关闭
source_files: [Linux 启动详细过程（开机启动顺序）.md]
---

# Linux 启动详细过程

Linux 系统从按下电源键到进入登录界面，经历10个步骤的启动流程。

---

## 启动流程概览

```
电源开启
    │
    ▼
① BIOS加载 ───► 硬件自检、启动顺序
    │
    ▼
② MBR读取 ───► 主引导记录（512字节）
    │
    ▼
③ Boot Loader ───► GRUB/LILO启动菜单
    │
    ▼
④ 内核加载 ───► 解压内核、start_kernel()
    │
    ▼
⑤ init进程 ───► /sbin/init读取inittab
    │
    ▼
⑥ 运行等级 ───► 设定运行级别（0-6）
    │
    ▼
⑦ rc.sysinit ───► 系统初始化脚本
    │
    ▼
⑧ 内核模块 ───► 装载modules.conf
    │
    ▼
⑨ 运行级别脚本 ───► rc0.d~rc6.d
    │
    ▼
⑩ rc.local ───► 用户自定义初始化
    │
    ▼
/bin/login ───► 登录界面
```

---

## 详细步骤

### 第一步：加载 BIOS

| 内容 | 说明 |
|------|------|
| 硬件信息 | CPU信息、设备启动顺序、硬盘信息、内存信息、时钟信息 |
| 启动顺序 | BIOS决定从哪个硬件设备启动（硬盘/光盘/U盘） |
| 自检 | POST（Power-On Self Test）硬件自检 |

### 第二步：读取 MBR

| 项目 | 说明 |
|------|------|
| 位置 | 硬盘第0磁道第一个扇区 |
| 大小 | 512字节 |
| 内容 | 预启动信息、分区表信息 |
| 加载 | 复制到物理内存 0x7c00 地址 |

**MBR 内容 = Boot Loader（GRUB/LILO）**

### 第三步：Boot Loader

| Boot Loader | 说明 |
|-------------|------|
| GRUB | 最常用，支持多系统启动菜单 |
| LILO | 传统启动器，使用较少 |
| spfdisk | 另一种启动器 |

**GRUB 作用**：
- 初始化硬件设备
- 建立内存映射图
- 提供启动菜单选择操作系统
- 读取配置文件（menu.lst/grub.lst）

### 第四步：加载内核

```
读取内核映像路径
    │
    ▼
解压缩内核映像
    │  输出: "Uncompressing Linux"
    │
    ▼
解压完成
    │  输出: "OK, booting the kernel"
    │
    ▼
调用 start_kernel()
    │
    ▼
初始化各种设备
    │
    ▼
Linux核心环境建立
```

### 第五步：init 进程

| 项目 | 说明 |
|------|------|
| 程序 | `/sbin/init`（第一个用户进程，PID=1） |
| 配置文件 | `/etc/inittab` |
| 主要任务 | 设定运行等级 |

### 第六步：运行等级

| 等级 | 说明 |
|------|------|
| 0 | 关机 |
| 1 | 单用户模式 |
| 2 | 无网络支持的多用户模式 |
| 3 | 有网络支持的多用户模式 |
| 4 | 保留，未使用 |
| 5 | 有网络支持有X-Window的多用户模式 |
| 6 | 重启 |

**配置形式**：`id:5:initdefault:` 表示运行等级5

### 第七步：rc.sysinit

| 任务 | 说明 |
|------|------|
| 设定PATH | 环境变量 |
| 网络配置 | `/etc/sysconfig/network` |
| 启动swap | 交换分区 |
| 设定/proc | 进程信息虚拟文件系统 |
| 其他 | 各种系统初始化 |

### 第八步：启动内核模块

依据 `/etc/modules.conf` 或 `/etc/modules.d` 装载内核模块。

### 第九步：运行级别脚本

根据运行级别，执行 `rc0.d` ~ `rc6.d` 目录下的脚本。

| 目录 | 说明 |
|------|------|
| `/etc/rc.d/rc0.d` | 关机级别脚本 |
| `/etc/rc.d/rc3.d` | 多用户级别脚本 |
| `/etc/rc.d/rc5.d` | 图形界面级别脚本 |

**脚本命名规则**：
- `K*`：Kill脚本（停止服务）
- `S*`：Start脚本（启动服务）

### 第十步：rc.local

| 特点 | 说明 |
|------|------|
| 执行时机 | 所有初始化工作完成后 |
| 作用 | 用户自定义初始化脚本 |
| 示例 | 启动自定义服务、设置环境 |

```bash
# /etc/rc.d/rc.local
# This script will be executed after all other init scripts.
# You can put your own initialization stuff here.
```

### 最后：登录界面

执行 `/bin/login`，等待用户输入用户名和密码。

---

## 与系统知识关联

| 系统模块 | 关联点 |
|----------|--------|
| [[summaries/linux-scheduler]] | init进程(PID=1)是调度起点 |
| [[concepts/task-struct]] | init是第一个进程结构体 |
| [[summaries/linux-irq-interrupt]] | 内核初始化设置中断处理 |
| [[summaries/linux-meminfo]] | 启动时内存初始化 |

---

## 相关链接

- [[summaries/linux-scheduler]] - 进程调度
- [[concepts/cfs-scheduler]] - CFS调度器
- [[summaries/linux-irq-interrupt]] - 中断机制