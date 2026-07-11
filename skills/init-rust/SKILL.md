---
name: init-rust
description: Generate opinionated Rust project config files (Cargo.toml with pedantic clippy lints, a fast-linker .cargo/config.toml) plus build-performance setup advice, for use with init-project
---

# init-rust

Generate Rust tooling configuration files for a new project. This skill is used by the `init-project` skill but can also be used standalone.

## Files to Generate

1. Copy `assets/Cargo.toml` into the target directory and substitute `<project-or-subdirectory-name>` with the actual project (or subdirectory) name. It ships pedantic clippy lints plus tuned `dev`/`release` profiles (thin debug info; LTO + `codegen-units = 1` + `strip` + `panic = "abort"` for release). Profiles only take effect in a workspace-root manifest — if you're initialising a workspace *member*, move the two `[profile.*]` tables to the root `Cargo.toml`.
2. Copy `assets/cargo-config.toml` to `.cargo/config.toml` at the project (or workspace) root. It wires **mold** as the linker on Linux targets (see below). Skip this file for a workspace member — a single `.cargo/config.toml` at the root covers the whole workspace.

## Build performance

These are the highest-leverage, low-effort speedups for Rust. The two levers are **caching compilation** (sccache) and **faster linking** (mold) — they're complementary: sccache skips recompiling unchanged crates, mold speeds up the link that happens even on a full cache hit.

### Faster linking — mold (Linux only)

Shipped in `assets/cargo-config.toml`, scoped to `x86_64/aarch64-unknown-linux-gnu`. mold is ~3-10x faster than GNU `ld` on large dependency graphs, and linking is exactly what sccache can't cache. Contributors on Linux (and CI/Docker Linux stages) must install mold: `apt-get install mold` / `dnf install mold` / `pacman -S mold`.

**macOS:** deliberately not configured. mold cannot link Mach-O (its macOS port `sold` was discontinued), and Apple's default linker (`ld-prime`, Xcode 15+) is already fast. Do **not** set a global `-fuse-ld=mold` — it will break native macOS builds. If you want a faster-than-default linker on older macOS, `lld` (`brew install llvm`, then `-fuse-ld=lld`) is the option, but on current Xcode the default is fine.

### Compilation caching — sccache (configure machine-wide, not in the repo)

sccache wraps `rustc` and caches compiled crates keyed on input hashes, giving cross-project reuse. Configure it in your **global** `~/.cargo/config.toml`, *not* the committed repo config — a repo-level `rustc-wrapper` forces every contributor to have sccache installed or their build breaks.

Install: `brew install sccache` (macOS) / `apt-get install sccache` / `cargo install sccache`. Then in `~/.cargo/config.toml`:

```toml
[build]
rustc-wrapper = "sccache"
```

Verify with `sccache --show-stats`. By default it uses a local disk cache.

**Shared remote cache (teams / CI / ephemeral build boxes):** point sccache at object storage so a warm cache is reused across machines. Set these in your shell env (read only when the sccache *server* starts — run `sccache --stop-server` after changing them):

```sh
# GCS example (S3/Redis/memcached/Azure backends also supported)
export SCCACHE_GCS_BUCKET=<your-bucket>
export SCCACHE_GCS_RW_MODE=READ_WRITE   # default is READ_ONLY — nothing is cached without this!
export SCCACHE_GCS_SERVICE_ACCOUNT=<sa-email>   # or SCCACHE_GCS_KEY_PATH
```

Gotchas worth knowing: the `READ_WRITE` line is the #1 thing people miss (default `READ_ONLY` silently caches nothing). Incremental debug builds of your own workspace crates are non-cacheable by design — sccache's win is on dependency crates. Prefix a clean release build with `CARGO_INCREMENTAL=0` to maximise the hit rate. Give the cache bucket a short object-lifecycle (e.g. 30-day delete) so it self-cleans.

### Release/dev profiles (shipped in Cargo.toml)

- `dev`: `debug = "line-tables-only"` keeps stack traces and panic locations but drops variable-level info, shrinking `target/` ~50-70%. Bump to `debug = true` per-project if you need full interactive debugging. (Optional extra on Linux/macOS: `split-debuginfo = "unpacked"`; omitted from the default because it's unsupported on some Windows setups.)
- `release`: `lto = true`, `codegen-units = 1`, `strip = true`, `panic = "abort"`. Note `panic = "abort"` disables `catch_unwind` and `cargo test --release` (plain `cargo test` is unaffected).

## Supply-chain protection

Cargo has no native minimum-release-age setting equivalent to pnpm's `minimumReleaseAge` or uv's `exclude-newer`. There is no opinionated default to ship here. For defence-in-depth, consider layering `cargo-deny` with the RustSec advisory database in projects that pull in third-party crates.
