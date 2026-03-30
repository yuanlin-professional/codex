# src/streaming/

## 功能概述

`streaming/` 模块提供 TUI 转录管线中的流式处理原语。

`StreamState` 拥有换行符门控的 Markdown 收集和已提交渲染行的 FIFO 队列。更高层模块在此基础上构建：
- **controller**：将排队的行适配为 `HistoryCell` 的消息流和计划流发射规则
- **chunking**：根据队列压力计算自适应排空计划
- **commit_tick**：将策略决策绑定到具体的控制器排空操作

关键不变量是队列排序：所有排空操作从队头弹出，入队时记录到达时间戳，使策略代码可以推断最老队列项的年龄。

自适应分块策略采用双挡位系统：
- **Smooth 模式**：稳定的基准显示节奏（每 tick 排空一行）
- **CatchUp 模式**：队列积压时立即排空全部队列，使显示延迟尽快收敛

## 目录结构

```
streaming/
├── mod.rs          # 模块入口，StreamState 和 QueuedLine 定义
├── controller.rs   # StreamController 和 PlanStreamController
├── chunking.rs     # AdaptiveChunkingPolicy 自适应分块策略
└── commit_tick.rs  # 提交 tick 编排，桥接 chunking 和 controller
```

## 核心接口/API

| 类型/函数 | 说明 |
|-----------|------|
| `StreamState` | 流状态，持有 Markdown 收集器和已提交行队列 |
| `StreamState::new(width, cwd)` | 创建新的流状态 |
| `StreamState::clear()` | 重置收集器和队列 |
| `StreamState::step()` | 从队头弹出一行 |
| `StreamController` | 流控制器，管理换行门控的流式传输、头部发射和提交动画 |
| `StreamController::push(delta)` | 推送文本增量 |
| `StreamController::finalize()` | 完成流式传输，排空并发射 |
| `PlanStreamController` | 计划流控制器（用于 AI 思考计划的流式显示） |
| `AdaptiveChunkingPolicy` | 自适应分块策略，管理 Smooth/CatchUp 模式切换 |
| `ChunkingMode` | 分块模式枚举（Smooth/CatchUp） |
| `DrainPlan` | 排空计划枚举（Single/Batch） |
| `QueueSnapshot` | 队列快照，用于策略决策 |
| `CommitTickScope` | 提交 tick 作用域（AnyMode/CatchUpOnly） |
| `CommitTickOutput` | 提交 tick 产生的输出 |
| `run_commit_tick()` | 执行一次提交 tick |
