# .codex -- Codex 项目级配置

## 功能概述

`.codex/` 目录存放 Codex 工具自身的项目级配置，主要包含 Skills（技能）定义。Skills 是 Codex 代理的可复用能力模块，每个 Skill 通过 `SKILL.md` 文件定义其名称、描述和详细指南，供 Codex 代理在特定场景下自动调用。

## 目录结构

```
.codex/
└── skills/
    ├── babysit-pr/
    │   ├── SKILL.md       # PR 监护技能定义
    │   ├── agents/        # 子代理定义
    │   ├── references/    # 参考资料
    │   └── scripts/       # 辅助脚本
    ├── remote-tests/
    │   └── SKILL.md       # 远程测试指南技能
    └── test-tui/
        └── SKILL.md       # TUI 交互测试指南技能
```

## Skills 说明

### babysit-pr
- **功能**：在 PR 创建后持续监控 CI 检查、review 评论和可合并状态，自动诊断失败、重试 flaky 测试、修复分支问题，直到 PR 可合并或需要人工介入
- **触发场景**：用户要求 Codex 监控 PR、观察 CI、处理 review 评论

### remote-tests
- **功能**：指导如何在远程执行器（Docker 容器）上运行集成测试
- **前提条件**：设置 `CODEX_TEST_REMOTE_ENV` 环境变量，通过 `./scripts/test-remote-env.sh` 构建和初始化 Docker 容器
- **限制**：目前仅支持 Linux 环境

### test-tui
- **功能**：指导如何交互式测试 Codex TUI
- **关键要点**：始终设置 `RUST_LOG="trace"`、使用 `-c log_dir=<目录>` 参数写入日志、使用 `just codex` 运行

## 依赖关系

- **被引用方**：Codex CLI 在运行时自动扫描 `.codex/skills/` 目录加载可用技能
- **格式约定**：每个技能目录必须包含 `SKILL.md` 文件，使用 YAML front-matter 定义 `name` 和 `description`
