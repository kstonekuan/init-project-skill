---
name: init-project
description: Scaffold a new project with opinionated tooling configs for TypeScript, Rust, Python, Docker, and/or Shell
argument-hint: "[languages: ts|rust|python|docker|shell...] [project-name]"
---

# init-project

Scaffold a new project directory with opinionated tooling configurations, code quality checks, and documentation.

Language-specific file generation is handled by companion skills: `init-typescript`, `init-rust`, and `init-python`. Use those skills for the language-specific steps below. Static templates for this skill live in `assets/`.

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

Concatenate `assets/gitignore/base` with one fragment per selected language. The base fragment is always included; add `assets/gitignore/typescript`, `assets/gitignore/rust`, and/or `assets/gitignore/python` as needed. Docker and Shell have no extra entries.

#### `AGENTS.md`

Copy `assets/AGENTS.md` verbatim.

#### `CLAUDE.md`

Create as a symlink pointing to `AGENTS.md`:

```bash
ln -s AGENTS.md CLAUDE.md
```

#### `CONTRIBUTING.md`

Copy `assets/CONTRIBUTING.md` and strip the language sections that aren't selected. Each language section is delimited by HTML comment markers, e.g. `<!-- BEGIN:TYPESCRIPT -->` ... `<!-- END:TYPESCRIPT -->`. For every language NOT selected, delete the marker block (markers and everything between them). For every language that IS selected, delete just the marker lines and keep the body.

The `## Code Style & Philosophy` section at the bottom is always kept as-is.

### 4. Generate Language-Specific Files

Use the companion skills to generate language-specific files:

- **TypeScript** → use the `init-typescript` skill
- **Rust** → use the `init-rust` skill
- **Python** → use the `init-python` skill

Place files in the appropriate directory (root for single-language, or the user-chosen subdirectory for multi-language).

### 5. Post-Setup

After creating all files:

1. If TypeScript was included, run `pnpm install` in the TypeScript directory to install devDependencies.
2. If Python was included, run `uv sync` in the Python directory.
3. Report what was created with a summary of the project structure.

The generated `pnpm-workspace.yaml` (`minimumReleaseAge: 10080`) and `[tool.uv] exclude-newer = "7 days"` settings enforce a 7-day minimum release age on dependencies as a supply-chain guard. Both installs above will respect this silently as long as pinned versions are old enough; if a chosen version is too new, the resolver will fail with a clear message and the user can either wait, pin an older version, or add the package to the exclude list.

Do NOT make an initial git commit — leave that to the user.
