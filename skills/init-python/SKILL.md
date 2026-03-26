---
name: init-python
description: Generate opinionated Python project config (pyproject.toml with ruff and ty) for use with init-project
---

# init-python

Generate Python tooling configuration files for a new project. This skill is used by the `init-project` skill but can also be used standalone.

## Files to Generate

### `pyproject.toml`

```toml
[project]
name = "<project-or-subdirectory-name>"
version = "0.1.0"
description = ""
requires-python = ">=3.13"

[dependency-groups]
dev = ["ruff>=0.15.0", "ty>=0.0.15"]

[build-system]
requires = ["uv_build>=0.10.0"]
build-backend = "uv_build"

[tool.ruff]
line-length = 100
target-version = "py313"

[tool.ruff.lint]
select = ["E", "W", "F", "I", "B", "C4", "UP", "ANN", "PTH", "RET", "SIM", "TID", "RUF"]
ignore = ["E501", "B008", "C901", "RET504", "ANN401"]

[tool.ruff.format]
quote-style = "double"
indent-style = "space"

[tool.ty.rules]
division-by-zero = "error"
deprecated = "error"
unused-ignore-comment = "error"
possibly-unresolved-reference = "error"
```
