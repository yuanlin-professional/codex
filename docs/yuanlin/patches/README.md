# patches -- 构建补丁文件

## 功能概述

`patches/` 目录包含 Codex 项目在构建过程中需要应用到第三方依赖或构建规则的补丁文件。这些补丁主要解决跨平台兼容性问题（特别是 Windows 和 musl 目标）、Bazel 构建集成问题以及上游依赖的适配需求。

## 目录结构

```
patches/
├── BUILD.bazel                                      # Bazel 构建文件
├── abseil_windows_gnullvm_thread_identity.patch      # Abseil Windows GNU LLVM 线程标识修复
├── aws-lc-sys_memcmp_check.patch                    # aws-lc-sys memcmp 检查补丁
├── llvm_windows_symlink_extract.patch               # LLVM Windows 符号链接提取修复
├── rules_rs_windows_gnullvm_exec.patch              # rules_rs Windows GNU LLVM 执行修复
├── rules_rust_windows_gnullvm_build_script.patch    # rules_rust Windows GNU LLVM 构建脚本修复
├── rusty_v8_prebuilt_out_dir.patch                  # rusty_v8 预构建输出目录补丁
├── v8_bazel_rules.patch                             # V8 Bazel 规则适配
├── v8_module_deps.patch                             # V8 模块依赖补丁
├── v8_source_portability.patch                      # V8 源码可移植性补丁
└── windows-link.patch                               # Windows 链接修复
```

## 补丁分类

### Windows 平台兼容性
| 补丁 | 说明 |
|------|------|
| `abseil_windows_gnullvm_thread_identity.patch` | 修复 Abseil 在 Windows GNU LLVM 环境下的线程标识问题 |
| `llvm_windows_symlink_extract.patch` | 修复 LLVM 在 Windows 上的符号链接提取问题 |
| `rules_rs_windows_gnullvm_exec.patch` | 修复 rules_rs 在 Windows GNU LLVM 下的执行问题 |
| `rules_rust_windows_gnullvm_build_script.patch` | 修复 rules_rust 在 Windows GNU LLVM 下的构建脚本 |
| `windows-link.patch` | 修复 Windows 平台的链接问题 |

### V8/rusty_v8 相关
| 补丁 | 说明 |
|------|------|
| `rusty_v8_prebuilt_out_dir.patch` | 调整 rusty_v8 预构建产物的输出目录 |
| `v8_bazel_rules.patch` | 适配 V8 的 Bazel 构建规则 |
| `v8_module_deps.patch` | 修复 V8 模块依赖关系 |
| `v8_source_portability.patch` | 提升 V8 源码的跨平台可移植性 |

### 其他
| 补丁 | 说明 |
|------|------|
| `aws-lc-sys_memcmp_check.patch` | 修复 aws-lc-sys 的 memcmp 检查问题 |

## 依赖关系

- **被引用方**：`MODULE.bazel`、Bazel 构建规则、CI 发布工作流
- **影响范围**：第三方 Cargo crate（rusty_v8、aws-lc-sys）、Bazel 规则（rules_rust、rules_rs）、V8 引擎源码
