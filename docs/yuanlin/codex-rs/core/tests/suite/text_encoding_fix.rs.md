# `text_encoding_fix.rs` -- 测试模块

## 测试覆盖范围
测试 shell 输出的文本编码检测和转换功能（issue #6178 的修复），验证 UTF-8、CP1251（Windows Cyrillic）、CP866（cmd.exe Cyrillic）和 Windows-1252（智能引号/破折号）等编码的正确解码。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `test_utf8_shell_output` | UTF-8 输出直接通过，不做转换 |
| `test_cp1251_shell_output` | CP1251 编码的西里尔文正确解码为 Unicode |
| `test_cp866_shell_output` | CP866 编码的西里尔文正确解码为 Unicode |
| `test_windows_1252_smart_decoding` | Windows-1252 智能引号/破折号正确解码 |
