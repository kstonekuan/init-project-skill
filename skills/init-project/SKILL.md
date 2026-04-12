---
name: init-project
description: Scaffold a new project with opinionated tooling configs for TypeScript, Rust, Python, Docker, and/or Shell
argument-hint: "[languages: ts|rust|python|docker|shell...] [project-name]"
---

# init-project

Scaffold a new project directory with opinionated tooling configurations, code quality checks, and documentation.

Language-specific file generation is handled by companion skills: `init-typescript`, `init-rust`, and `init-python`. Use those skills for the language-specific steps below.

## Argument Parsing

Parse the arguments from the user's invocation. Arguments are space-separated and can appear in any order.

**Language tokens** (case-insensitive):
- `ts`, `typescript` → TypeScript
- `rust` → Rust
- `python`, `py` → Python
- `docker` → Docker
- `shell`, `bash`, `sh` → Shell

**Project name**: The remaining non-language token. If not provided, ask the user for a project name.

If no languages are specified, ask the user which languages to include.

## Execution Flow

### 1. Determine Project Layout

**Single language**: Everything goes at the project root.

**Multiple languages**: Ask the user two questions:
1. "Should each language live in its own subdirectory, or should everything be at the project root?"
2. If subdirectories: "What should each subdirectory be named?" (ask for each language — do NOT assume defaults like `app/` or `server/`)

### 2. Create Project Directory

Create the project directory at `./<project-name>/` relative to the current working directory.

Initialize git:
```bash
git init <project-name>
```

### 3. Generate Shared Files (always created at project root)

#### `.gitignore`

Generate a `.gitignore` with entries conditional on the selected languages:

**Always include:**
```
.DS_Store
.env
.env.*
!.env.example
```

**If TypeScript:**
```
node_modules/
dist/
.turbo/
```

**If Rust:**
```
target/
```

**If Python:**
```
__pycache__/
*.pyc
.venv/
*.egg-info/
```

#### `AGENTS.md`

Create with this exact content:
```markdown
Read [CONTRIBUTING.md](./CONTRIBUTING.md).
```

#### `CLAUDE.md`

Create as a symlink pointing to `AGENTS.md`:
```bash
ln -s AGENTS.md CLAUDE.md
```

#### `CONTRIBUTING.md`

Generate adapted to the included languages. Use this template:

```markdown
# Contributing

## Code Quality

{include only sections for selected languages}
```

**If TypeScript is included:**
```markdown
### TypeScript

```bash
pnpm lint       # Biome linting with auto-fix
pnpm typecheck  # TypeScript type checking
pnpm check      # Run all checks
```
```

**If Rust is included:**
```markdown
### Rust

```bash
cargo clippy --all-targets --all-features  # Linting
cargo fmt                                   # Formatting
```
```

**If Python is included:**
```markdown
### Python

```bash
uv run ruff check --fix  # Linting with auto-fix
uv run ruff format       # Code formatting
uv run ty check          # Type checking
```
```

**If Docker is included:**
```markdown
### Docker

```bash
hadolint Dockerfile*  # Dockerfile linting
```
```

**If Shell is included:**
```markdown
### Shell Scripts

```bash
shellcheck **/*.sh  # Shell script linting
```
```

**Always include this full section after Code Quality:**

```markdown
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
```

### 4. Generate Language-Specific Files

Use the companion skills to generate language-specific files:

- **TypeScript** → use the `init-typescript` skill
- **Rust** → use the `init-rust` skill
- **Python** → use the `init-python` skill

Place files in the appropriate directory (root for single-language, or the user-chosen subdirectory for multi-language).

### 5. Post-Setup

After creating all files:

1. If TypeScript was included, run `pnpm install` in the TypeScript directory to install devDependencies
2. If Python was included, run `uv sync` in the Python directory
3. Report what was created with a summary of the project structure

Do NOT make an initial git commit — leave that to the user.
