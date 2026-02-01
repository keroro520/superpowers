---
name: rust-verification
description: Use when verifying Rust code changes during development - use cargo check instead of full build/clippy/test, run targeted tests only when test code is modified
---

# Rust Verification (Lightweight Mode)

## Overview

**Core principle:** `cargo check` for fast iteration; defer clippy and full test to branch completion.

## Quick Reference

| Change Type | Command |
|-------------|---------|
| Regular code | `cargo check` |
| Test code modified | `cargo check && cargo test <module>::tests` |
| Cargo.toml / build.rs | `cargo check` |

**Test code = files in `tests/`, `*_test.rs`, or containing `#[test]`**

## The Rule

```
CARGO CHECK MUST PASS BEFORE CLAIMING TASK COMPLETE
```

- Exit code 0 = pass
- Warnings acceptable (clippy catches them later)

## Deferred to Branch Completion

Run these in `superpowers:finishing-a-development-branch`:

```
/cargo.clippy
/cargo.test
```

## Flowchart

```dot
digraph rust_verification {
    rankdir=TB;
    "Code changed" [shape=box];
    "Test code?" [shape=diamond];
    "cargo check" [shape=box];
    "cargo check && cargo test <module>" [shape=box];
    "Exit 0?" [shape=diamond];
    "Done" [shape=box, style=filled, fillcolor=lightgreen];
    "Fix errors" [shape=box];

    "Code changed" -> "Test code?";
    "Test code?" -> "cargo check && cargo test <module>" [label="yes"];
    "Test code?" -> "cargo check" [label="no"];
    "cargo check && cargo test <module>" -> "Exit 0?";
    "cargo check" -> "Exit 0?";
    "Exit 0?" -> "Done" [label="yes"];
    "Exit 0?" -> "Fix errors" [label="no"];
    "Fix errors" -> "Test code?";
}
```

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Using `cargo build` | Use `cargo check` (no codegen, ~10x faster) |
| Full test suite every change | Only test affected modules |
| Skipping tests when test code changes | Always run `cargo test <module>` |
