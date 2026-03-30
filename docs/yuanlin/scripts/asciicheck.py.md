# `asciicheck.py` -- 检查并修复文件中的非 ASCII 字符

## 功能概述

该脚本用于扫描指定文件列表，检测其中是否包含非 ASCII 字符（如不间断空格 U+00A0、智能引号、长破折号等）。这些不可见字符会导致正则表达式匹配失败，或使 GitHub 渲染 Markdown 时生成意外的锚点值。脚本支持 `--fix` 模式，可自动将常见非 ASCII 字符替换为对应的 ASCII 等价形式。少数被明确列入白名单的 Unicode 码点（如 U+2728 sparkles）会被豁免检查。

## 关键函数

| 函数 | 说明 |
|------|------|
| `main()` | 入口函数，解析命令行参数（`--fix` 及文件列表），遍历文件逐一执行检查 |
| `lint_utf8_ascii(filename, fix)` | 核心检查逻辑：读取文件内容，逐字符扫描非 ASCII 码点，报告位置信息；若启用 `--fix` 则执行替换并回写文件 |

## 使用方式

```bash
# 检查模式（只报告不修改）
python scripts/asciicheck.py file1.md file2.md

# 修复模式（自动替换可识别的非 ASCII 字符）
python scripts/asciicheck.py --fix file1.md file2.md
```

检测到非 ASCII 字符时返回退出码 `1`，否则返回 `0`。

## 实现备注

- `substitutions` 字典定义了 10 种常见 Unicode 字符到 ASCII 的映射（不间断空格、各类引号、破折号、省略号等）。
- `allowed_unicode_codepoints` 集合中的码点不会触发报错，目前仅包含 U+2728（sparkles）。
- 文件先以二进制模式读取再尝试 UTF-8 解码，若解码失败会报告字节偏移和行列号。
- `--fix` 模式只替换 `substitutions` 中定义的字符，其余非 ASCII 字符仍会被报告但不会被修改。
