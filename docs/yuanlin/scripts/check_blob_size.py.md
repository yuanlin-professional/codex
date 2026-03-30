# `check_blob_size.py` -- 检查 Git 变更文件的 blob 大小是否超限

## 功能概述

该脚本用于 CI 流水线中，对比两个 Git 版本（base 与 head）之间变更的文件，检查每个文件的 blob 大小是否超过配置的上限（默认 500 KiB）。被加入白名单的文件会被豁免检查。脚本同时支持将检查结果写入 GitHub Actions 的 Step Summary，以便在 PR 页面中直观展示每个文件的大小和状态。

## 关键函数

| 函数 | 说明 |
|------|------|
| `main()` | 入口函数，解析 `--base`、`--head`、`--max-bytes`、`--allowlist` 参数，执行检查并输出结果 |
| `run_git(*args)` | 执行 git 子命令并返回标准输出 |
| `load_allowlist(path)` | 从白名单文件加载允许超限的文件路径集合（支持 `#` 注释） |
| `get_changed_paths(base, head)` | 获取两个版本间新增或修改的文件列表（使用 `--diff-filter=AM`） |
| `is_binary_change(base, head, path)` | 通过 `git diff --numstat` 判断文件变更是否为二进制类型 |
| `blob_size(commit, path)` | 使用 `git cat-file -s` 获取指定版本中文件的字节大小 |
| `collect_changed_blobs(base, head, allowlist)` | 收集所有变更文件的 blob 信息（路径、大小、是否在白名单、是否为二进制） |
| `write_step_summary(max_bytes, blobs, violations)` | 将检查结果以 Markdown 表格形式写入 `GITHUB_STEP_SUMMARY` |
| `format_kib(size_bytes)` | 将字节数格式化为 KiB 字符串 |

## 使用方式

```bash
python scripts/check_blob_size.py \
  --base origin/main \
  --head HEAD \
  --max-bytes 512000 \
  --allowlist .github/blob-size-allowlist.txt
```

超限文件存在时返回退出码 `1`，并提示将文件路径添加到白名单或缩小文件体积。

## 实现备注

- 使用 `ChangedBlob` 数据类封装每个变更文件的元数据。
- 白名单文件中每行一个路径，支持 `#` 行内注释。
- `GITHUB_STEP_SUMMARY` 环境变量为空时跳过摘要写入，使脚本可在本地运行。
- 二进制文件判断基于 `git diff --numstat` 输出中增删行数均为 `-` 的特征。
