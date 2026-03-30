# `turnOptions.ts` -- 回合级配置选项

`turnOptions.ts` 定义了单次回合（turn）的配置选项类型 `TurnOptions`。包含 `outputSchema`（JSON Schema，用于指定代理输出的结构化格式）和 `signal`（AbortSignal，用于取消正在进行的回合）两个可选字段。这些选项在每次调用 `run()` 或 `runStreamed()` 时传入，粒度比线程选项更细。
