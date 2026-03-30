# `async.py` -- 模型选择与 Turn 参数组合示例（异步版）

演示动态模型选择和多种 turn 参数的组合使用。首先通过 `codex.models(include_hidden=True)` 获取完整模型列表，使用 `_pick_highest_model()` 选择最优模型和 `_pick_highest_turn_effort()` 选择最高推理努力级别。然后在两个 turn 中分别展示：基本参数（model + effort）和全参数（加上 approval_policy、cwd、output_schema、personality、sandbox_policy、summary）。
