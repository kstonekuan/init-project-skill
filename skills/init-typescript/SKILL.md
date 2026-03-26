---
name: init-typescript
description: Generate opinionated TypeScript project config files (package.json, tsconfig.json, biome.json) for use with init-project
---

# init-typescript

Generate TypeScript tooling configuration files for a new project. This skill is used by the `init-project` skill but can also be used standalone.

## Files to Generate

### `package.json`

```json
{
  "name": "<project-or-subdirectory-name>",
  "version": "0.0.0",
  "private": true,
  "type": "module",
  "scripts": {
    "dev": "",
    "build": "",
    "lint": "biome check --write",
    "typecheck": "tsc --noEmit",
    "check": "pnpm run lint && pnpm run typecheck"
  },
  "devDependencies": {
    "@biomejs/biome": "latest",
    "typescript": "latest"
  }
}
```

If Rust is co-located in the same repo, ask the user: "Do you want cargo check commands (clippy, fmt) wired into the package.json scripts?"

If yes, add these scripts (adjust `--manifest-path` if Rust is in a subdirectory):
```json
"cargo:clippy": "cargo clippy --manifest-path <path>/Cargo.toml --all-targets --all-features",
"cargo:fmt": "cargo fmt --manifest-path <path>/Cargo.toml",
"cargo": "pnpm run cargo:clippy && pnpm run cargo:fmt",
"check": "pnpm run lint && pnpm run typecheck && pnpm run cargo"
```

### `tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "lib": ["DOM", "DOM.Iterable", "ESNext"],
    "jsx": "react-jsx",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "noEmit": true,
    "strict": true,
    "noImplicitReturns": true,
    "noImplicitOverride": true,
    "noFallthroughCasesInSwitch": true,
    "noUncheckedIndexedAccess": true,
    "noPropertyAccessFromIndexSignature": true,
    "useUnknownInCatchVariables": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "incremental": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"]
}
```

### `biome.json`

```json
{
  "$schema": "./node_modules/@biomejs/biome/configuration_schema.json",
  "vcs": { "enabled": true, "clientKind": "git", "useIgnoreFile": true },
  "files": { "ignoreUnknown": true },
  "formatter": { "enabled": true, "indentStyle": "tab" },
  "linter": {
    "enabled": true,
    "rules": {
      "recommended": true,
      "correctness": {
        "noUnusedVariables": "error",
        "noUnusedImports": "error",
        "noUnusedFunctionParameters": "error"
      },
      "suspicious": { "noExplicitAny": "warn" }
    }
  },
  "javascript": { "formatter": { "quoteStyle": "double" } },
  "assist": { "enabled": true, "actions": { "source": { "organizeImports": "on" } } }
}
```

### `src/` directory

Create an empty `src/` directory:
```bash
mkdir -p src
```
