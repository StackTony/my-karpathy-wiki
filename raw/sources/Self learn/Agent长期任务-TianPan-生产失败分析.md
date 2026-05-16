· 阅读需 11 分钟

大多数 AI Agent 演示（demo）运行得都非常完美。

它们在 30 秒内运行完毕，调用三个工具，并返回整洁的结果。然后，有人要求 Agent 执行一些真正重要的事情——交叉引用代码库、运行多阶段数据流水线、处理批量文档——于是整个过程在超时、部分状态和重复副作用的级联反应中土崩瓦解。

问题不在于模型，而在于基础设施。运行几分钟或几小时的 Agent 与在几秒钟内完成的 Agent 相比，面临着完全不同的一类系统问题。大多数团队在最糟糕的时间点撞上了这堵墙：在他们已经发布了用户依赖的产品之后。

## 解释大多数 Agent 失败原因的数学逻辑

在设计 Agent 工作流时，有一个数字值得牢记：步骤数（step count）。不是任务时长，而是步骤数。

一个每步成功率为 95% 且包含 10 个步骤的工作流，其最终成功的概率约为 60%。到了 20 个步骤，这一比例降至 36%。到了 30 个步骤，成功率则低于 21%。即使你将每一步的可靠性提高到 99%，一个包含 20 个步骤的工作流仍有近 18% 的失败率。

这不是模型质量问题。这是应用于顺序系统的复合概率，且其影响是残酷的。解决方法不是寻找更好的模型，而是围绕数学逻辑进行设计。更短、有界的工作流优于开放式的规划。具有幂等性的单步重试优于端到端的重新运行。阶段边界的检查点（Checkpoints）意味着你可以恢复某一步，而不是重启所有工作。

一项针对 26 个领域的 306 名生产环境 AI 从业者的研究发现，68% 的人出于这个原因，将其 Agent 限制在有界工作流中，而不是开放式规划。在演示中，无边界的自主性听起来令人印象深刻。但在生产环境中，这意味着你无法预测失败率。

## 30 秒同步边界

同步 HTTP 适用于在 30 秒内完成的 AI 任务。客户端阻塞，Agent 运行，响应返回。简单且可靠。

超过这个边界，一切都会崩溃。负载均衡器超时。移动客户端断开连接。Serverless 函数触碰执行限制。用户刷新页面并触发第二次请求。在 Agent 运行于 localhost 且没有超时约束的本地开发环境中，这些问题都不会暴露出来。

事后看来，正确的模式显而易见：立即返回任务 ID，然后让客户端轮询或订阅 Webhook 以获取完成状态。Agent 在后台 worker 中运行，与发起的 HTTP 请求没有连接。这就是每一个可靠的长时运行任务系统的运作方式——后台 worker、任务队列、异步处理。

AI Agent 的不同之处在于必须随任务一起管理的所有内容：

- **LLM 上下文** 需要被序列化并存储，而不是保存在进程内存中
- **工具调用结果** 需要持久化，以便重试时不会重新执行产生副作用
- **进度信号** 需要送达早已与发起请求断开连接的前端
- **双重速率限制** ——每分钟请求数和每分钟 Token 数——要求队列能够计算“下个任务何时可以安全运行”，而不仅仅是对 429 错误做出反应

那些通过标准任务队列路由 AI 任务而不考虑这些问题的团队，最终不得不自己构建审计层（accounting layer）。或者，他们在遇到生产事故后，被动地进行构建。

## 检查点不等于持久化执行

大多数 Agent 框架都提供某种形式的检查点机制。例如，LangGraph 的 `PostgresSaver` 会在每个节点持久化图状态，以便可以恢复失败的工作流。这确实很有用，而且远比没有好。

但检查点和持久化执行（durable execution）并不是一回事，这种区别在生产环境中至关重要。

检查点机制是说：“我已经保存了你的状态。接下来的交给你了。”如果一个 worker 崩溃，必须由人或某种机制来检测故障、获取检查点，并使用正确的线程标识符手动触发恢复。如果两个 worker 尝试同时恢复同一个线程，你就会得到重复执行。框架提供持久化，你提供编排。

持久化执行则是说：“你的工作流一定会运行到完成。句号。”运行时通过心跳检测故障，自动回放事件历史以重建状态，并在发生故障的精确步骤恢复，而不会重新运行已完成的工作。无需自定义看门狗（watchdog）逻辑，无需手动处理分布式协调。

像 Temporal 这样的工具代表了持久化执行模型。一个最初在 LangGraph 和 Redis 上构建生产 Agent 系统的团队将结果描述为“概念上很强大，但实践中很脆弱”——他们迁移到 Temporal 正是为了消除一直以来维护的自定义重试和恢复逻辑。Trigger.dev 和 Inngest 等更新的专用平台也在追求类似的模式，并提供了更贴合 AI 场景的人机工程学设计。

对于大多数正在构建新项目的团队来说，实际的问题是：你愿意承担多少编排逻辑？如果你有能力在之上构建故障检测和恢复，带有 `PostgresSaver` 的 LangGraph 是一个合理的生产选择。如果你希望这些由系统为你处理，那么持久化执行运行时值得其带来的运维开销。

## 幂等性是首要关注的问题

破坏检查点（Checkpointing）和重试逻辑的隐蔽故障模式是非幂等副作用。当一个 Agent 发送电子邮件、写入数据库或调用支付 API，然后在更新检查点之前 Worker 崩溃时，下一次重试将尝试再次执行相同的操作。

防止这种情况的模式在分布式系统中已经非常成熟：每次外部写入都携带一个与工作流标识和步骤编号绑定的幂等键（Idempotency Key）。如果同一个键被提交两次，第二次调用就是一个返回原始结果的空操作（No-op）。大多数现代 API 都原生支持这一点。失败点通常在于调用 API 时未包含幂等键的 Agent，因为它们在设计时没有考虑到重试场景。

这会产生实际后果。复合故障场景 —— Agent 编造了错误信息，并以此调用下游 API，调用在执行一半时失败，随后重试重新执行整个序列 —— 可能会在多个系统中触发一系列重复的副作用。即使无法防止底层的幻觉，在每个外部写入步骤使用幂等键也可以防止重复副作用，即便它们无法阻止潜在的幻觉。

实践清单：每次外部写入都需要一个幂等键。每个队列在入队时都应进行去重。每次重试都应从持久化状态中提取上下文，而不是从头开始重建。

## 人机协作（Human-in-the-Loop）是系统设计决策

长时间运行 Agent 中的人工监管通常被视为 UX 问题 —— 在执行风险操作之前显示确认对话框。实际上，这是一个具有重大架构影响的系统设计决策。

暂停一个多步 Agent 工作流以等待人工响应，意味着 Worker 必须释放其执行上下文，存储足够的状态以便干净地恢复，并无限期地等待 —— 可能是几小时或几天。这不是大多数任务队列实现的工作方式。Worker 被期望完成任务或使任务失败。“等待输入”是大多数系统在设计之初并未考虑处理的第三种状态。

原生支持此功能的框架通过明确的中断机制来实现。LangGraph 的 `interrupt()` 函数在定义的节点处暂停图，保存状态，并在输入到达时干净地恢复。Temporal 通过工作流可以无限期阻塞的 Signal 处理器来支持它。核心属性是“等待人工”是执行模型中的一等状态（First-class state），而不是通过在热循环中轮询数据库来实现的变通方法。

值得设计到工作流拓扑中的中断点包括：

- **置信度阈值异常** ：当 Agent 对意图不确定时，在猜测之前先询问。
- **不可逆操作边界** ：在删除、修改生产系统或触发支付之前。
- **成本检查点** ：在产生大量 Token 消耗的工作流中，在继续之前先在里程碑处验证进度。
- **模糊输入** ：与其幻想一个模糊指令的解决方案，不如将模糊性暴露出来。

要做到这一点，需要在实现之前将中断点设计到工作流拓扑中，而不是在生产环境出问题后才作为事后补救措施添加。

## 35 分钟衰减问题

研究人员观察并命名了一个经验性的可靠性悬崖，但尚未能完全解释：AI Agent 在连续运行约 35 分钟后，可靠性会下降。主要的假设是上下文窗口饱和以及累积的推理漂移 —— 每个决策都包含了之前所有决策的噪声，而这些噪声会产生复合效应。

实际的影响是，长周期任务不应实现为单个连续的 Agent 运行。它们应该被分解为具有明确移交点的会话（Sessions）。

Anthropic 自身处理多会话编码任务的方法很有启发性。初始化 Agent 在第一个会话中建立基础状态。后续会话运行一个编码 Agent，它从 git 提交历史和持久化的进度文档中重建上下文，然后朝着目标取得增量进展。每个会话都是有边界的。上下文是从持久化的产物（Artifacts）中重建的，而不是从实时上下文窗口中继承的。

这种模式 —— 有边界的会话、用于连续性的外部状态、明确的移交产物 —— 适用于大多数长周期任务领域。运行了两小时的 Agent 并不比两个各运行一小时且有干净移交的 Agent 更强大。它可能能力更弱，而且在出问题时更难调试。

## 大多数团队容易犯的错误

实际生产部署中的模式是一致的：团队构建了一个在演示中运行良好的原型，将其发布给用户，发现它在长任务中崩溃，然后围绕一个并非为此设计的架构追溯性地修补异步基础设施。

这种事后修补总是比最初就正确构建要昂贵得多，因为同步假设被硬编码的地方往往是核心部分 —— API 响应契约、前端状态管理、错误处理、成本归属。

长时间运行的 Agent 任务实际需要的基础设施并不新颖。任务队列、持久化执行、幂等键和人机协作工作流在分布式系统中都是已解决的问题。新颖之处在于将它们应用于 AI 特定的工作负载，这些工作负载中的上下文管理、双重速率限制（Dual rate limiting）和非确定性故障模式增加了摩擦。框架和平台正迅速围绕处理这些摩擦的模式进行标准化 —— 但从一开始就设计异步的根本决策权仍在于工程师。

如果你正在构建一个处理任务超过 30 秒的 Agent，请优先考虑异步设计。在运行任何操作之前返回一个任务 ID。持久化恢复所需的每一部分状态。确保每一次外部写入都是幂等的。明确定义你的人工中断点。限制你的会话长度。这些不是以后再添加的优化 —— 它们是让其他一切变得可靠的基石。

**References:**
- [https://workos.com/blog/mcp-async-tasks-ai-agent-workflows](https://workos.com/blog/mcp-async-tasks-ai-agent-workflows)
- [https://www.diagrid.io/blog/checkpoints-are-not-durable-execution-why-langgraph-crewai-google-adk-and-others-fall-short-for-production-agent-workflows](https://www.diagrid.io/blog/checkpoints-are-not-durable-execution-why-langgraph-crewai-google-adk-and-others-fall-short-for-production-agent-workflows)
- [https://temporal.io/blog/build-resilient-agentic-ai-with-temporal](https://temporal.io/blog/build-resilient-agentic-ai-with-temporal)
- [https://blog.logrocket.com/ai-agent-task-queues/](https://blog.logrocket.com/ai-agent-task-queues/)
- [https://www.prodigaltech.com/blog/why-most-ai-agents-fail-in-production](https://www.prodigaltech.com/blog/why-most-ai-agents-fail-in-production)
- [https://eunomia.dev/blog/2025/05/11/checkpointrestore-systems-evolution-techniques-and-applications-in-ai-agents/](https://eunomia.dev/blog/2025/05/11/checkpointrestore-systems-evolution-techniques-and-applications-in-ai-agents/)
- [https://zylos.ai/research/2026-03-04-ai-agent-workflow-checkpointing-resumability](https://zylos.ai/research/2026-03-04-ai-agent-workflow-checkpointing-resumability)
- [https://www.permit.io/blog/human-in-the-loop-for-ai-agents-best-practices-frameworks-use-cases-and-demo](https://www.permit.io/blog/human-in-the-loop-for-ai-agents-best-practices-frameworks-use-cases-and-demo)
- [https://zylos.ai/research/2026-01-16-long-running-ai-agents](https://zylos.ai/research/2026-01-16-long-running-ai-agents)
- [https://arxiv.org/html/2512.08769v1](https://arxiv.org/html/2512.08769v1)
**Let's stay in touch and Follow me for more thoughts and updates**