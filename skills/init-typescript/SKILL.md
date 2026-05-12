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

Then create an empty `src/` directory:

```bash
mkdir -p src
```

## Co-located Rust

If Rust is in the same repo, ask the user: "Do you want cargo check commands (clippy, fmt) wired into the package.json scripts?"

If yes, add these scripts to `package.json` (adjust `--manifest-path` if Rust is in a subdirectory):

```json
"cargo:clippy": "cargo clippy --manifest-path <path>/Cargo.toml --all-targets --all-features",
"cargo:fmt": "cargo fmt --manifest-path <path>/Cargo.toml",
"cargo": "pnpm run cargo:clippy && pnpm run cargo:fmt",
"check": "pnpm run lint && pnpm run typecheck && pnpm run cargo"
```
