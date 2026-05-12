---
title: CRI (Container Runtime Interface)
created: 2026-05-12
updated: 2026-05-12
tags: [Kubernetes, CRI, 容器运行时接口]
---

# CRI 容器运行时接口

Kubernetes 定义的标准接口，用于 kubelet 与容器运行时解耦。

## 背景

早期 Kubernetes 硬编码调用 Docker API。为支持更多运行时，Google/红帽推出 CRI 标准。

## API 结构

```
CRI (gRPC):
├── ImageService
│   ├── PullImage
│   ├── ListImages
│   └── RemoveImage
└── RuntimeService
│   ├── CreatePodSandbox
│   ├── CreateContainer
│   ├── StartContainer
│   ├── StopContainer
│   └── ExecSync
```

## Shim 垫片模式

未实现 CRI 的运行时通过 shim 适配：

```
kubelet → CRI shim → 容器运行时
```

| Shim | 运行时 |
|------|--------|
| dockershim | Docker (已废弃) |
| CRI-Containerd | containerd 1.0 |
| 内置插件 | containerd 1.1+ |

## dockershim 废弃

Kubernetes 1.24 移除内置 dockershim：
- 调用链过长：kubelet→dockershim→Docker Daemon→containerd→runc
- 可用 cri-dockerd 或直接用 containerd

## 接口配置

```bash
# kubelet 参数
--container-runtime-endpoint=/run/containerd/containerd.sock
--image-service-endpoint=/run/containerd/containerd.sock
```

## 相关链接

- [[summaries/containerd-runtime]]
- [[concepts/containerd]]
- [[concepts/oci]]