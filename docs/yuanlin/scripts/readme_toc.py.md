# `readme_toc.py` -- 检查并生成 Markdown 文件的目录（Table of Contents）

## 功能概述

该脚本用于验证 Markdown 文件中 `<!-- Begin ToC -->` 和 `<!-- End ToC -->` 标记之间的目录列表是否与文件中的实际标题一致。在检查模式下，若目录过期则输出 unified diff 并返回非零退出码；在 `--fix` 模式下，自动重新生成目录内容并回写文件。该脚本通常作为 CI 检查步骤运行，确保 README 的目录始终与内容同步。

## 关键函数

| 函数 | 说明 |
|------|------|
| `main()` | 入口函数，解析文件路径和 `--fix` 参数，调用 `check_or_fix` |
| `generate_toc_lines(content)` | 从 Markdown 内容中提取 `##` 至 `######` 级标题，生成带缩进和锚点链接的目录行列表 |
| `check_or_fix(readme_path, fix)` | 核心逻辑：定位 ToC 标记，提取现有目录，与新生成的目录对比；若不一致则根据模式报错或修复 |

## 使用方式

```bash
# 检查默认的 README.md
python scripts/readme_toc.py

# 检查指定文件
python scripts/readme_toc.py docs/guide.md

# 自动修复目录
python scripts/readme_toc.py --fix README.md
```

## 实现备注

- 锚点（slug）生成逻辑：先将非 ASCII 空格和破折号归一化，再移除非字母数字字符，最后将空格替换为连字符，与 GitHub 的标题锚点生成规则保持一致。
- 代码块内（由 ``` 包围）的标题行会被自动跳过，避免误识别。
- 若文件中找不到 ToC 标记，脚本会打印提示信息并正常退出（返回 0），不会导致 CI 失败。
- 生成目录时排除 ToC 区域本身，防止自引用。
