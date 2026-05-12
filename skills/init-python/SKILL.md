---
name: init-python
description: Generate opinionated Python project config (pyproject.toml with ruff, ty, and uv supply-chain guard) for use with init-project
---

# init-python

Generate Python tooling configuration files for a new project. This skill is used by the `init-project` skill but can also be used standalone.

## Files to Generate

Copy `assets/pyproject.toml` into the target directory. Substitute `<project-or-subdirectory-name>` with the actual project (or subdirectory) name.