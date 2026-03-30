# shell-command

## 功能概述

`codex-shell-command` 是 Codex 的 Shell 命令解析与安全检测工具库，跨多个 Codex crate 共享使用。它提供了以下核心能力：

1. **命令解析**：将 AI 模型生成的任意命令字符串解析为结构化的 `ParsedCommand`，提取可执行文件、参数、工作目录等元数据
2. **安全判断**：判断命令是否是"安全"的（只读操作）或"危险"的（可能造成破坏性影响）
3. **Shell 检测**：自动识别 Bash 和 PowerShell 命令格式
4. **Bash/PowerShell 解析**：使用 tree-sitter 语法解析器对 Bash 脚本进行精确解析

## 架构说明

```
                  +-------------------+
                  |  shell-command    |
                  |    (lib.rs)       |
                  +---------+---------+
                            |
          +-----------------+------------------+
          |                 |                  |
          v                 v                  v
  +-------+-------+ +------+--------+ +-------+--------+
  | parse_command  | |command_safety | |  shell_detect   |
  | (命令解析)      | | (安全检测)     | | (Shell 类型识别) |
  +-------+-------+ +------+--------+ +-------+--------+
          |                 |
          v                 |
  +-------+-------+        |
  |     bash      |        +---> is_safe_command
  | (tree-sitter  |        +---> is_dangerous_command
  |  Bash 解析)    |        +---> windows_safe_commands
  +---------------+        +---> powershell_parser
  +---------------+
  |  powershell   |
  | (PowerShell   |
  |  命令提取)     |
  +---------------+

依赖:
    codex-protocol   (ParsedCommand 类型定义)
    tree-sitter      (Bash 语法解析)
    tree-sitter-bash (Bash 语法定义)
```

## 目录结构

```
shell-command/
  src/
    lib.rs                              # 模块声明与公共导出
    parse_command.rs                    # 核心命令解析逻辑（非常庞大）
    bash.rs                             # Bash 命令提取与 tree-sitter 解析
    powershell.rs                       # PowerShell 命令提取
    shell_detect.rs                     # Shell 类型自动检测
    command_safety/
      mod.rs                            # 安全检测模块入口
      is_safe_command.rs                # 安全命令判断（只读操作）
      is_dangerous_command.rs           # 危险命令判断（破坏性操作）
      windows_safe_commands.rs          # Windows 平台安全命令列表
      windows_dangerous_commands.rs     # Windows 平台危险命令列表
      powershell_parser.rs              # PowerShell 命令解析辅助
      powershell_parser.ps1             # PowerShell 解析脚本
  Cargo.toml
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-protocol` | 定义 `ParsedCommand` 等协议类型 |
| `codex-utils-absolute-path` | 绝对路径处理 |
| `tree-sitter` | 通用语法解析框架 |
| `tree-sitter-bash` | Bash 语法定义 |
| `shlex` | Shell 词法分析（引号处理、转义等） |
| `regex` | 正则表达式匹配 |
| `once_cell` | 延迟初始化（如 tree-sitter Parser） |
| `base64` | Base64 编解码 |
| `url` | URL 解析 |
| `which` | 查找可执行文件路径 |
| `serde` / `serde_json` | 序列化支持 |

## 核心接口/API

### 命令安全检测

```rust
/// 判断命令是否是安全的（只读操作，不会修改系统状态）
pub fn is_safe_command(command: &[String]) -> bool;

/// 判断命令是否是危险的（可能造成不可逆的破坏性影响）
pub fn is_dangerous_command(command: &[String]) -> bool;
```

### 命令解析

```rust
/// 将任意命令解析为结构化的 ParsedCommand 列表
/// 支持管道、重定向、命令组合等复杂场景
pub fn parse_command(command: &[String]) -> Vec<ParsedCommand>;

/// Shell 词法合并
pub fn shlex_join(tokens: &[String]) -> String;

/// 提取 Shell 和脚本内容
pub fn extract_shell_command(command: &[String]) -> Option<(&str, &str)>;
```

### Shell 检测

```rust
/// 提取 Bash 命令：识别 bash -c "..." 或 sh -c "..." 模式
pub fn extract_bash_command(command: &[String]) -> Option<(&str, &str)>;

/// 提取 PowerShell 命令
pub fn extract_powershell_command(command: &[String]) -> Option<(&str, &str)>;
```

### Bash 解析

基于 tree-sitter 的 Bash 脚本解析，能够精确识别：
- 简单命令与参数
- 管道与重定向
- 子 shell 与命令替换
- 变量赋值
- 循环与条件语句
