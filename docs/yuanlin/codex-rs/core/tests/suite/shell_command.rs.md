# `shell_command.rs` -- 测试模块

## 测试覆盖范围
测试 shell_command 工具的基本执行功能，包括标准输出、login/non-login shell 模式、多行输出、管道命令、超时处理和 Unicode 输出等场景。使用 TestCodexHarness 进行测试。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `shell_command_works` | 基本 echo 命令执行，验证输出格式（exit code、wall time、output） |
| `output_with_login` | login=true 模式下执行 echo 命令 |
| `output_without_login` | login=false 模式下执行 echo 命令 |
| `multi_line_output_with_login` | login 模式下的多行输出正确性 |
| `pipe_output_with_login` | 管道命令（echo \| cat）在默认 login 模式下的输出（仅 Unix） |
| `pipe_output_without_login` | 管道命令在 login=false 模式下的输出（仅 Unix） |
| `shell_command_times_out_with_timeout_ms` | 命令超过 timeout_ms 后被终止，exit code 为 124 |
| `unicode_output` | Unicode 字符（naïve_café）在 login/non-login 模式下正确输出 |
| `unicode_output_with_newlines` | 包含换行和 Unicode 的混合输出正确处理 |
