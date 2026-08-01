# Execution Context: Python

**Language Rules (Python):**
- Testing: use `pytest`; write the failing test first (RED), then minimal implementation (GREEN). Run `pytest <path>::<test> -v` and expect PASS with no warnings.
- Types: add type hints on public functions; run `mypy` (or `pyright`) if configured and expect it to pass.
- Formatting/linting: code must pass `ruff` / `black` (or the project's configured formatter and linter).
- Imports: prefer stdlib; lazy-import optional heavy/optional dependencies inside the function that needs them so the module imports without them.
- Errors: raise specific exceptions with actionable messages; don't swallow exceptions silently.
- Environments: assume a virtualenv; pin dependencies in the project's `requirements.txt` / `pyproject.toml`.
