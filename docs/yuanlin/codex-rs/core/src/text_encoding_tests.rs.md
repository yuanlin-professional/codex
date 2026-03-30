# `text_encoding_tests.rs` — 测试模块

## 测试覆盖范围

该测试模块覆盖 `bytes_to_string_smart` 函数的智能文本编码检测与转换功能，涵盖 UTF-8 直通路径、多种 Windows 代码页（CP1251/CP866/CP1252 等）、ISO-8859 系列编码（2-13）、CJK 编码（Shift-JIS/GBK/EUC-KR/Big5）、ANSI 转义序列保留以及无效字节回退行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_utf8_passthrough` | 验证有效 UTF-8 文本直接通过不复制 |
| `test_cp1251_russian_text` | 验证 Windows-1251 编码的俄文西里尔字母正确解码 |
| `test_cp1251_privet_word` | 回归测试：CP1251 的"Привет"不被错误识别为 Windows-1252 |
| `test_koi8_r_privet_word` | 验证 KOI8-R 编码的西里尔文本正确解码 |
| `test_cp866_russian_text` | 验证 CP866（cmd.exe 遗留控制台）编码的俄文正确解码 |
| `test_cp866_uppercase_text` | 验证 IBM866 启发式对大写西里尔字母的正确处理 |
| `test_cp866_uppercase_followed_by_ascii` | 回归测试：CP866 大写字母后跟 ASCII 不被误判为 CP1252 |
| `test_windows_1252_quotes` | 验证 Windows-1252 智能标点符号映射为 Unicode 字符 |
| `test_windows_1252_multiple_quotes` | 验证较长的 Windows-1252 标点片段正确转换 |
| `test_windows_1252_privet_gibberish_is_preserved` | 验证 Windows-1252 无法编码的西里尔乱码原样保留 |
| `test_iso8859_1_latin_text` | 验证 ISO-8859-1（Latin-1）文本正确解码 |
| `test_iso8859_2_central_european_text` | 验证 ISO-8859-2 中欧文本正确解码 |
| `test_iso8859_3_south_europe_text` | 验证 ISO-8859-3 南欧文本（马耳他语/世界语）正确解码 |
| `test_iso8859_4_baltic_text` | 验证 ISO-8859-4 波罗的海/北欧文本正确解码 |
| `test_iso8859_5_cyrillic_text` | 验证 ISO-8859-5 西里尔文本正确解码 |
| `test_iso8859_6_arabic_text` | 验证 ISO-8859-6 阿拉伯文本正确解码 |
| `test_iso8859_7_greek_text` | 验证 ISO-8859-7 希腊文本正确解码 |
| `test_iso8859_8_hebrew_text` | 验证 ISO-8859-8 希伯来文本正确解码 |
| `test_iso8859_9_turkish_text` | 验证 ISO-8859-9（Windows-1254）土耳其文本正确解码 |
| `test_iso8859_10_nordic_text` | 验证 ISO-8859-10 北欧文本正确解码 |
| `test_iso8859_11_thai_text` | 验证 ISO-8859-11（Windows-874）泰文正确解码 |
| `test_iso8859_13_baltic_text` | 验证 ISO-8859-13 波罗的海文本正确解码 |
| `test_windows_1250_central_european_text` | 验证 Windows-1250 中欧文本正确解码 |
| `test_windows_1251_encoded_text` | 验证 Windows-1251 编码的俄文长文本正确解码 |
| `test_windows_1253_greek_text` | 验证 Windows-1253 希腊文本正确解码 |
| `test_windows_1254_turkish_text` | 验证 Windows-1254 土耳其文本正确解码 |
| `test_windows_1255_hebrew_text` | 验证 Windows-1255 希伯来文本正确解码 |
| `test_windows_1256_arabic_text` | 验证 Windows-1256 阿拉伯文本正确解码 |
| `test_windows_1257_baltic_text` | 验证 Windows-1257 波罗的海文本正确解码 |
| `test_windows_1258_vietnamese_text` | 验证 Windows-1258 越南文本正确解码 |
| `test_windows_874_thai_text` | 验证 Windows-874 泰文正确解码 |
| `test_windows_932_shift_jis_text` | 验证 Shift-JIS（Windows-932）日文正确解码 |
| `test_windows_936_gbk_text` | 验证 GBK（Windows-936）中文正确解码 |
| `test_windows_949_korean_text` | 验证 EUC-KR（Windows-949）韩文正确解码 |
| `test_windows_950_big5_text` | 验证 Big5（Windows-950）繁体中文正确解码 |
| `test_latin1_cafe` | 验证 Latin-1 字节（如 cafe 中的重音符号）正确解码 |
| `test_preserves_ansi_sequences` | 验证 ANSI 转义序列在编码检测过程中被保留 |
| `test_fallback_to_lossy` | 验证完全无效字节序列回退到 lossy UTF-8 转换 |
