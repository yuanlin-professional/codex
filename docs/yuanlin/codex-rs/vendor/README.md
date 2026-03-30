# codex-rs/vendor -- 供应商代码

## 功能概述

`codex-rs/vendor/` 目录存放 Codex Rust 工作空间直接引入的第三方 C 源码。这些代码以供应商（vendoring）方式包含在项目中，而非通过系统包管理器安装，以确保构建的可重复性和跨平台一致性。

## 目录结构

```
vendor/
├── BUILD.bazel          # Bazel 构建定义
└── bubblewrap/          # Bubblewrap 沙箱工具源码
    ├── bubblewrap.c     # 主程序入口
    ├── bind-mount.c/.h  # 绑定挂载实现
    ├── network.c/.h     # 网络命名空间实现
    ├── utils.c/.h       # 工具函数
    ├── meson.build      # Meson 构建文件（原始构建系统）
    ├── meson_options.txt # Meson 构建选项
    ├── bwrap.xml        # man page 源文件
    ├── COPYING          # 许可证（LGPL v2+）
    ├── LICENSE          # 许可证
    ├── README.md        # 上游 README
    ├── CODE-OF-CONDUCT.md
    ├── SECURITY.md
    ├── NEWS.md
    ├── release-checklist.md
    ├── ci/              # 上游 CI 配置
    ├── completions/     # Shell 补全文件
    ├── demos/           # 示例演示
    ├── packaging/       # 打包相关文件
    ├── tests/           # 测试用例
    ├── uncrustify.cfg   # 代码格式化配置
    └── uncrustify.sh    # 代码格式化脚本
```

## 组件说明

### Bubblewrap (bwrap)
- **项目地址**：https://github.com/containers/bubblewrap
- **用途**：为 Codex 的 Linux 沙箱（sandbox）功能提供低级容器隔离能力。Bubblewrap 是一个非特权的用户命名空间沙箱工具，Codex 使用它在 Linux 上隔离命令执行环境
- **集成方式**：源码直接编译，通过 Bazel BUILD 文件定义构建规则
- **许可证**：LGPL v2+

## 依赖关系

- **被引用方**：`codex-rs/linux-sandbox` crate，用于 Linux 平台的进程沙箱化
- **构建系统**：通过 `BUILD.bazel` 在 Bazel 中编译，或被 Cargo 构建脚本引用
- **平台限制**：仅用于 Linux 平台
