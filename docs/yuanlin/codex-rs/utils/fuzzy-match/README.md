# fuzzy-match

## 功能概述

`codex-utils-fuzzy-match` 实现了大小写不敏感的模糊子序列匹配算法，用于 Codex 的文件/命令过滤等场景。算法具备完整的 Unicode 支持，能正确处理大小写转换导致字符数量变化的情况。

评分机制：
- 匹配窗口越紧凑，分数越低（越好）
- 前缀匹配额外获得 -100 分的加成
- 空 needle 匹配任意字符串，返回 `i32::MAX` 分

## 目录结构

```
fuzzy-match/
├── Cargo.toml
└── src/
    └── lib.rs          # fuzzy_match() 和 fuzzy_indices() 函数
```

## 依赖关系

无外部依赖，仅使用标准库。

## 核心接口/API

### `fuzzy_match(haystack: &str, needle: &str) -> Option<(Vec<usize>, i32)>`

对 `haystack` 进行大小写不敏感的子序列匹配。

- 返回 `Some((indices, score))` -- `indices` 是匹配字符在原始 haystack 中的位置，`score` 越小越好
- 返回 `None` -- needle 不是 haystack 的子序列

```rust
// 基本匹配
let (idx, score) = fuzzy_match("hello", "hl").unwrap();
assert_eq!(idx, vec![0, 2]);

// 大小写不敏感
let (idx, _) = fuzzy_match("FooBar", "foO").unwrap();
assert_eq!(idx, vec![0, 1, 2]);

// Unicode 支持
let (idx, _) = fuzzy_match("Istanbul", "is").unwrap();
```

### `fuzzy_indices(haystack: &str, needle: &str) -> Option<Vec<usize>>`

`fuzzy_match` 的便捷封装，仅返回匹配位置索引，不返回分数。
