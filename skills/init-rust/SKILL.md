---
name: init-rust
description: Generate opinionated Rust project config files (Cargo.toml with pedantic clippy lints) for use with init-project
---

# init-rust

Generate Rust tooling configuration files for a new project. This skill is used by the `init-project` skill but can also be used standalone.

## Files to Generate

### `Cargo.toml`

```toml
[package]
name = "<project-or-subdirectory-name>"
version = "0.1.0"
edition = "2021"

[dependencies]

[lints.clippy]
pedantic = { level = "warn", priority = -1 }
needless_pass_by_value = "allow"
unnecessary_wraps = "allow"
missing_panics_doc = "allow"
```

### `src/main.rs`

```rust
fn main() {
    println!("Hello, world!");
}
```
