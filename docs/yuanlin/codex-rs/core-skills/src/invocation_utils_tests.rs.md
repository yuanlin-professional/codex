# `invocation_utils_tests.rs` — 测试模块

## 测试覆盖范围
该文件测试隐式技能调用检测工具函数，包括脚本运行检测和文档读取检测的各种场景。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `script_run_detection_matches_runner_plus_extension` | 检测运行器+脚本扩展名组合 |
| `script_run_detection_excludes_python_c` | 排除 `python -c` 内联执行模式 |
| `skill_doc_read_detection_matches_absolute_path` | 通过绝对路径匹配技能文档读取 |
| `skill_script_run_detection_matches_relative_path_from_skill_root` | 从技能根目录匹配相对路径脚本运行 |
| `skill_script_run_detection_matches_absolute_path_from_any_workdir` | 从任意工作目录匹配绝对路径脚本运行 |
