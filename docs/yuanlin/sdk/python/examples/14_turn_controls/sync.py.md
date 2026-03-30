# `sync.py` -- Turn 控制操作示例（同步版）

演示 turn 的两种控制操作：`steer`（转向）和 `interrupt`（中断）。第一个 turn 启动后立即调用 `steer()` 发送新的指令，改变模型的行为方向；第二个 turn 启动后立即调用 `interrupt()` 中断执行。两个 turn 都通过 `stream()` 消费事件直到完成，并输出各自的状态、事件计数和助手文本预览。
