# src/onboarding/auth/

## 功能概述

`auth/` 子目录包含引导流程中认证步骤的具体实现。目前包含无头 ChatGPT 登录的实现，即不需要浏览器交互的设备码（Device Code）认证流程。

## 目录结构

```
auth/
└── headless_chatgpt_login.rs    # 无头 ChatGPT 设备码登录实现
```

## 核心接口/API

| 类型/函数 | 说明 |
|-----------|------|
| 无头 ChatGPT 登录 | 通过设备码流程进行 ChatGPT 认证，无需浏览器交互 |
