# `pkce.rs` — PKCE 码生成
实现 OAuth PKCE（Proof Key for Code Exchange）挑战码生成。使用 64 字节随机数生成 URL-safe Base64 编码的 code_verifier，并用 SHA-256 计算对应的 code_challenge。
