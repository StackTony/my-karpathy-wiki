---
title: 中断监控工具
created: 2026-05-11
updated: 2026-05-11
tags: [中断, interrupts, IRQ, DFX]
sources: [DFX工具/==中断==]
---

# 中断监控工具

Linux 中断实时监控脚本和分析方法。

## 实时中断观测脚本

该脚本动态观测每秒中断数变化，支持阈值过滤：

```bash
#!/bin/bash
th=1   # 中断数量屏蔽阈值

function Clean() {
    [[ -e pre ]] && rm -f pre
    [[ -e cur ]] && rm -f cur
    exit 0
}
trap Clean SIGHUP SIGINT SIGTERM

CPUS=$(lscpu | grep '^CPU(s):' | awk '{print $NF}')
while true; do
    echo -en "\033[1;33mIgnore delta <= ${th} / s\033[0m\n\n"
    cat /proc/interrupts | grep -E ':([[:space:]]*[0-9]+){3,}' | grep -v arch_timer > pre
    sleep 1
    cat /proc/interrupts | grep -E ':([[:space:]]*[0-9]+){3,}' | grep -v arch_timer > cur
    
    # diff 分析前后中断量差异
    diff pre cur | ...
done
```

### 脚本功能
- 每 1 秒采集 `/proc/interrupts`
- 计算每秒中断增量
- 过滤 arch_timer（避免高频干扰）
- 支持阈值屏蔽（只显示 delta > threshold）

## 手动中断分析

### 查看中断统计
```bash
cat /proc/interrupts
```

### 使用 irqtop（如有）
```bash
irqtop
```

## 中断异常分析场景

当 kvmtop EXTirq 指标异常时：
1. 对比正常和问题时期的中断量差异
2. 关注核间中断（IPI）：
   - IPI_RESCHEDULE（调度中断）
   - IPI_CALL_FUNC（函数调用中断）
3. 关注 virtio 设备中断

## 相关链接

- [[summaries/linux-irq-interrupt]]
- [[summaries/kvmtop-tool]]
- [[summaries/dfx-tools-overview]]