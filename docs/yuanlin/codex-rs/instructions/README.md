# instructions

## 功能概述

`codex-instructions` 是 Codex 项目中用户指令和技能指令的载体 crate。它定义了 Codex 提示词系统中上下文用户片段(contextual user fragments)的标记协议,以及将 AGENTS.md 和 SKILL.md 内容序列化为模型可理解格式的逻辑。

核心职责包括:

- **片段标记协议**: 定义 AGENTS.md 和 Skill 两种上下文用户片段的起止标记,用于在对话历史中标识和匹配用户指令。
- **UserInstructions**: 将目录关联的 AGENTS.md 内容序列化为带标记的文本,并转换为 `ResponseItem`。
- **SkillInstructions**: 将技能的名称、路径和内容序列化为带标记的 XML 格式文本。

## 架构说明

该 crate 设计精简,由两个核心模块组成:

1. **fragment.rs** -- 上下文片段定义:
   - `ContextualUserFragmentDefinition`: 通用片段定义,包含 `start_marker` 和 `end_marker`。
   - `AGENTS_MD_FRAGMENT`: AGENTS.md 片段,起始标记为 `# AGENTS.md instructions for `,结束标记为 `</INSTRUCTIONS>`。
   - `SKILL_FRAGMENT`: 技能片段,起始标记为 `<skill>`,结束标记为 `</skill>`。
   - 提供 `matches_text()` 方法用于判断文本是否为某类片段。
   - 提供 `wrap()` 方法用于将正文内容包裹在标记中。
   - 提供 `into_message()` 方法将片段转换为 `ResponseItem::Message`。

2. **user_instructions.rs** -- 指令数据结构:
   - `UserInstructions`: 包含 `directory`(关联目录)和 `text`(AGENTS.md 内容),序列化格式为:
     ```
     # AGENTS.md instructions for <directory>

     <INSTRUCTIONS>
     <text>
     </INSTRUCTIONS>
     ```
   - `SkillInstructions`: 包含 `name`、`path`、`contents`,序列化格式为:
     ```
     <skill>
     <name>...</name>
     <path>...</path>
     ...contents...
     </skill>
     ```
   - 两者均实现 `From<...> for ResponseItem`,方便直接注入对话上下文。

## 目录结构

```
instructions/
  Cargo.toml
  src/
    lib.rs                      # 模块声明与公开导出
    fragment.rs                 # ContextualUserFragmentDefinition, AGENTS_MD/SKILL 片段
    user_instructions.rs        # UserInstructions, SkillInstructions 数据结构
    user_instructions_tests.rs  # 单元测试
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-protocol` | `ContentItem`, `ResponseItem` 协议类型 |
| `serde` | 序列化/反序列化 |

## 核心接口/API

### 片段定义

```rust
pub struct ContextualUserFragmentDefinition {
    start_marker: &'static str,
    end_marker: &'static str,
}

impl ContextualUserFragmentDefinition {
    pub fn matches_text(&self, text: &str) -> bool;
    pub fn wrap(&self, body: String) -> String;
    pub fn into_message(self, text: String) -> ResponseItem;
}

/// AGENTS.md 片段标记
pub const AGENTS_MD_FRAGMENT: ContextualUserFragmentDefinition;

/// Skill 片段标记
pub const SKILL_FRAGMENT: ContextualUserFragmentDefinition;
```

### 指令结构

```rust
/// AGENTS.md 用户指令
pub struct UserInstructions {
    pub directory: String,
    pub text: String,
}

impl UserInstructions {
    pub fn serialize_to_text(&self) -> String;
}

/// 技能指令
pub struct SkillInstructions {
    pub name: String,
    pub path: String,
    pub contents: String,
}

/// 用户指令前缀常量
pub const USER_INSTRUCTIONS_PREFIX: &str;
```
