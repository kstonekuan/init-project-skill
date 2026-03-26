# init-project

Agent skills for scaffolding new projects with opinionated tooling configs.

## Install

```bash
# Install all skills
npx skills add kstonekuan/init-project-skill

# Install specific skills
npx skills add kstonekuan/init-project-skill --skill init-project
npx skills add kstonekuan/init-project-skill --skill init-typescript
```

## Skills

| Skill | Description |
|-------|-------------|
| `init-project` | Core orchestration — argument parsing, project layout, git init, shared files (.gitignore, AGENTS.md, CLAUDE.md, CONTRIBUTING.md) |
| `init-typescript` | package.json, tsconfig.json, biome.json with strict settings |
| `init-rust` | Cargo.toml with pedantic clippy lints, src/main.rs |
| `init-python` | pyproject.toml with ruff and ty configuration |

## Usage

```
/init-project ts rust my-app
/init-project python
/init-project ts my-frontend
```

The core `init-project` skill delegates to the language-specific skills (`init-typescript`, `init-rust`, `init-python`) for generating tooling configs. Language skills can also be used standalone.

## Opinions

- **TypeScript**: Biome for linting/formatting, strict tsconfig, pnpm
- **Rust**: Pedantic clippy lints with select relaxations
- **Python**: Ruff for linting/formatting, ty for type checking, uv for package management
- **Code style**: Explicit types, exhaustive pattern matching, verbose naming
