# Contributing

## Code Quality

```bash
lychee --offline --include-fragments --extensions md .
```

<!-- BEGIN:TYPESCRIPT -->
### TypeScript

```bash
pnpm format
pnpm check
```
<!-- END:TYPESCRIPT -->

<!-- BEGIN:RUST -->
### Rust

Install once:

```bash
cargo install cargo-machete --version 0.9.2 --locked
cargo install cargo-deny --version 0.20.2 --locked
```

```bash
cargo fmt
cargo machete --with-metadata --skip-target-dir
cargo deny check bans
cargo clippy --workspace --all-targets --all-features -- -D warnings
cargo test --workspace --all-features
```

For an on-demand dependency review, start with `cargo tree`. In a workspace,
`cargo tree --workspace --depth workspace --edges normal` is one way to focus on
dependencies between workspace crates. Choose the packages, features and targets
that match what you want to inspect.
<!-- END:RUST -->

<!-- BEGIN:PYTHON -->
### Python

```bash
uv run ruff check --fix  # Linting with auto-fix
uv run ruff format       # Code formatting
uv run ty check          # Type checking
```
<!-- END:PYTHON -->

<!-- BEGIN:DOCKER -->
### Docker

```bash
hadolint Dockerfile*  # Dockerfile linting
```
<!-- END:DOCKER -->

<!-- BEGIN:SHELL -->
### Shell Scripts

```bash
shellcheck **/*.sh  # Shell script linting
```
<!-- END:SHELL -->

## Code Style & Philosophy

### Typing & Pattern Matching

- Prefer **explicit types** over raw dicts -- make invalid states unrepresentable where practical
- Prefer **typed variants over string literals** when the set of valid values is known
- Use **exhaustive pattern matching** (`match` in Python and Rust, `ts-pattern` in TypeScript) so the type checker can verify all cases are handled
- Structure types to enable exhaustive matching when handling variants
- Prefer **shared internal functions over factory patterns** when extracting common logic from hooks or functions -- keep each export explicitly defined for better IDE navigation and readability

### Forward Compatibility

- **Unknown values**: Parse to an explicit `Unknown*` variant (never `None`), log at warn level, preserve raw data, gracefully ignore instead of raising exception

### Self-Documenting Code

- **Verbose naming**: Variable and function naming should read like documentation
- **Strategic comments**: Only for non-obvious logic or architectural decisions; avoid restating what code shows
