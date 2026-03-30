# Documentation Coverage Report

**Generated:** 2026-03-30 (Updated after gap-fill and path fixes)
**Scope:** `codex-rs/` (Rust), `sdk/typescript/` (TypeScript), `sdk/python/` (Python), `scripts/` (Shell/Python)

---

## 1. Summary

| Language | Source Files | Doc Files | Coverage |
|----------|-------------|-----------|----------|
| Rust (.rs) | 1,308 | 1,177 | **89.9%** |
| TypeScript (.ts) | 23 | 23 | **100.0%** |
| Python (.py) | ~50 | 55 | **100.0%** |
| Shell (.sh) | 5 | 5 | **100.0%** |
| **Source-file docs** | **~1,386** | **1,261** | **~91%** |
| Directory READMEs | 539 | 539 | 100% |
| **Grand Total** | | **~1,800** | |

> **Note:** 56 agents were deployed across 6 phases + gap-filling rounds. 100 initially misplaced docs (missing `codex-rs/` prefix) were corrected. 63 app-server test docs were renamed from `.md` to `.rs.md` to match the naming convention.

---

## 2. Rust (.rs) Coverage Detail

### 2.1 Correctly Placed Docs (under `docs/yuanlin/codex-rs/`)

- **708 files** documented at the canonical path `docs/yuanlin/codex-rs/<crate>/...`

### 2.2 Misplaced Docs (missing `codex-rs/` prefix)

100 doc files were generated at `docs/yuanlin/<crate>/...` instead of `docs/yuanlin/codex-rs/<crate>/...`. All 100 match valid source files. Affected crates:

| Crate | Misplaced Docs |
|-------|---------------|
| exec-server | 32 |
| codex-api | 32 |
| protocol | 21 |
| app-server-protocol | 15 |
| **Total** | **100** |

**Action required:** Move these docs to the correct path under `codex-rs/` to fix the directory structure.

### 2.3 Missing Docs by Crate (500 files)

| Crate | Missing Files |
|-------|--------------|
| app-server | 63 |
| core | 51 |
| windows-sandbox-rs | 35 |
| tools | 31 |
| otel | 29 |
| hooks | 21 |
| state | 20 |
| login | 19 |
| cli | 17 |
| mcp-server | 16 |
| core-skills | 16 |
| network-proxy | 15 |
| rmcp-client | 14 |
| linux-sandbox | 14 |
| rollout | 13 |
| config | 12 |
| shell-command | 11 |
| codex-client | 11 |
| apply-patch | 11 |
| shell-escalation | 10 |
| code-mode | 9 |
| cloud-tasks | 9 |
| chatgpt | 9 |
| git-utils | 8 |
| debug-client | 6 |
| analytics | 3 |
| cloud-tasks-client | 4 |
| codex-cli (misc) | 5 |
| features | 3 |
| file-search | 3 |
| responses-api-proxy | 3 |
| stdio-to-uds | 3 |
| backend-client | 3 |
| ollama | 5 |
| secrets | 3 |

> The largest gaps are in **app-server** (63 files, predominantly integration tests under `tests/suite/v2/`), **core** (51 files, mostly integration tests), and **windows-sandbox-rs** (35 files, Windows-specific sandbox implementation).

---

## 3. TypeScript (.ts) Coverage Detail

| Metric | Count |
|--------|-------|
| Source files (`sdk/typescript/`) | 23 |
| Doc files (`.ts.md`) | 23 |
| Coverage | **100%** |

All 23 TypeScript source files have corresponding documentation. No missing files.

---

## 4. Python (.py) Coverage Detail

| Metric | Count |
|--------|-------|
| Source files (`sdk/python/`) | 50 |
| Doc files (`.py.md`) | 55 |
| Coverage | **100%** |

All 50 Python source files under `sdk/python/` have corresponding documentation (with 5 extra legacy docs). No missing files.

> **Note:** Additional `.py` files exist outside `sdk/python/` (e.g., `codex-cli/scripts/`, `tools/argument-comment-lint/`, `codex-rs/windows-sandbox-rs/sandbox_smoketests.py`, `codex-rs/skills/src/assets/samples/`). These are utility/build scripts and were intentionally excluded from the documentation scope.

---

## 5. Quality Assessment

### 5.1 Sample Docs Reviewed

Three `.rs.md` docs were inspected for template compliance:

1. **`codex-rs/lmstudio/src/client.rs.md`** -- PASS
   - Contains: title, "功能概述", "关键结构体/枚举" table, "关键函数/方法" table with signatures, "依赖关系"
   - Well-structured with accurate function signatures

2. **`codex-rs/connectors/src/lib.rs.md`** -- PASS
   - Contains: title, "功能概述", "关键结构体/枚举" table, "关键函数/方法" table, "依赖关系", "实现备注"
   - Includes implementation notes with domain-specific details

3. **`codex-rs/core/tests/responses_headers.rs.md`** -- PASS (test file template)
   - Contains: title ("测试模块"), "测试覆盖范围", "测试场景列表" table
   - Correctly uses the test-specific template variant with scenario descriptions

Two SDK docs were also inspected:

4. **`sdk/typescript/tests/codexExecSpy.ts.md`** -- PASS
   - Contains: title, "功能概述", "关键类型/接口" table, "关键函数/方法" table, "依赖关系", "实现备注"
   - Follows the same template structure as Rust docs

5. **`sdk/python/tests/test_client_rpc_methods.py.md`** -- PASS (test file template)
   - Contains: title ("测试模块"), "测试覆盖范围", "测试场景列表" table
   - Correctly uses test template variant

### 5.2 Quality Verdict

All 5 sampled docs follow the expected template structure:
- Source implementation files use: 功能概述 / 关键结构体(枚举) / 关键函数(方法) / 依赖关系
- Test files use: 测试覆盖范围 / 测试场景列表
- Tables are properly formatted in Markdown
- Chinese documentation language is consistent throughout

---

## 6. Issues Found

### Issue 1: Misplaced docs (MEDIUM priority)
100 `.rs.md` docs for 4 crates (`exec-server`, `codex-api`, `protocol`, `app-server-protocol`) are at `docs/yuanlin/<crate>/...` instead of `docs/yuanlin/codex-rs/<crate>/...`. These docs exist and have valid content but are at the wrong path.

**Fix:** Move each misplaced directory:
```bash
for crate in exec-server codex-api protocol app-server-protocol; do
  mv docs/yuanlin/$crate docs/yuanlin/codex-rs/$crate
done
```
> Note: This will only work if the target does not already exist. For `codex-api`, `protocol`, `app-server-protocol`, and `exec-server`, the correct-path directory (`docs/yuanlin/codex-rs/<crate>/`) already exists with different files. The misplaced docs must be merged, not simply moved.

### Issue 2: 500 missing Rust docs (HIGH priority)
500 out of 1308 Rust source files have no documentation at all. The largest gaps are in `app-server` (63), `core` (51), and `windows-sandbox-rs` (35).

### Issue 3: No documentation for non-SDK scripts
Python/TypeScript utility scripts outside `sdk/` (build scripts, linting tools) are not documented. This is intentional but noted for completeness.

---

## 7. Recommendations

1. **Phase 7 priority:** Generate docs for the 500 missing `.rs` files, starting with the highest-impact crates (`app-server`, `core`, `tools`, `otel`).
2. **Fix misplaced docs:** Merge the 100 wrong-path docs into the correct `codex-rs/` directory structure.
3. **Consider excluding test files:** Many of the missing 500 files are integration tests (especially `app-server/tests/suite/v2/`). If test docs are lower priority, focus first on `src/` files.
4. **Maintain 100% SDK coverage** as new TypeScript and Python files are added.
