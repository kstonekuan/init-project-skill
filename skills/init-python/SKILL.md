---
name: init-python
description: Generate opinionated Python project config (pyproject.toml with ruff, ty, and uv supply-chain guard) for use with init-project
---

# init-python

Generate Python tooling configuration files for a new project. This skill is used by the `init-project` skill but can also be used standalone.

## Files to Generate

Copy `assets/pyproject.toml` into the target directory. Substitute `<project-or-subdirectory-name>` with the actual project (or subdirectory) name.

If no package exists, create an empty `src/<module_name>/__init__.py`, deriving the module name from the project name with hyphens replaced by underscores, before installing the project. Preserve existing source files.

After installing dependencies, run `uv run ruff check --fix`, `uv run ruff format`, and `uv run ty check`. CI checks use `uv run ruff check`, `uv run ruff format --check`, and `uv run ty check`.
