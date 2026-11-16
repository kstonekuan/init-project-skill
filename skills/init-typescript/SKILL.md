---
name: init-typescript
description: Generate opinionated TypeScript project config files (package.json, tsconfig.json, biome.json, pnpm-workspace.yaml) for use with init-project
---

# init-typescript

Generate TypeScript tooling configuration files for a new project. This skill is used by the `init-project` skill but can also be used standalone.

## Files to Generate

Copy each template from `assets/` into the target directory. Substitute `<project-or-subdirectory-name>` in `assets/package.json` with the actual project (or subdirectory) name.

| Asset | Destination |
|---|---|
| `assets/package.json` | `package.json` (substitute name) |
| `assets/tsconfig.json` | `tsconfig.json` |
| `assets/biome.json` | `biome.json` |
| `assets/pnpm-workspace.yaml` | `pnpm-workspace.yaml` |

Create `src/` and, if no application entry exists, create `src/index.ts` containing `export {};` so the generated type check has an input:

```bash
mkdir -p src
```

## Co-located Rust

If Rust is in the same repo, ask the user: "Do you want cargo check commands (clippy, fmt) wired into the package.json scripts?"

If yes, add these scripts to `package.json` (replace `<path>` with the Rust workspace path relative to package.json, using `.` when co-located):

```json
"cargo:clippy": "cargo clippy --manifest-path <path>/Cargo.toml --workspace --all-targets --all-features -- -D warnings",
"cargo:fmt": "cargo fmt --manifest-path <path>/Cargo.toml --all -- --check",
"cargo:machete": "cargo machete --with-metadata --skip-target-dir <path>",
"cargo": "pnpm run cargo:fmt && pnpm run cargo:machete && pnpm run cargo:clippy",
"check": "pnpm run lint && pnpm run typecheck && pnpm run knip && pnpm run cargo"
```

When used standalone, create a `.gitignore` containing `node_modules/` and `dist/` if none exists before running Biome. Run `pnpm format` and `pnpm check` after installation. Configure Knip entry points for the actual application or exported package API; keep exceptions scoped to confirmed external consumers. For co-located Rust, run Machete from the Rust workspace directory and install it as specified in `init-rust`.
