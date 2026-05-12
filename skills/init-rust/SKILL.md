---
name: init-rust
description: Generate opinionated Rust project config files (Cargo.toml with pedantic clippy lints) for use with init-project
---

# init-rust

Generate Rust tooling configuration files for a new project. This skill is used by the `init-project` skill but can also be used standalone.

## Files to Generate

Copy `assets/Cargo.toml` into the target directory and substitute `<project-or-subdirectory-name>` with the actual project (or subdirectory) name.

## Supply-chain protection

Cargo has no native minimum-release-age setting equivalent to pnpm's `minimumReleaseAge` or uv's `exclude-newer`. There is no opinionated default to ship here. For defence-in-depth, consider layering `cargo-deny` with the RustSec advisory database in projects that pull in third-party crates.
