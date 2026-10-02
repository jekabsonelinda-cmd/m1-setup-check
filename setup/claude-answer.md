# What `make test` does

`make test` runs a single command, `pytest -q` (`Makefile:10-11`).

## What it runs

- `pytest -q` runs pytest in quiet mode, so the output is short: dots for passing tests and a one-line summary.
- The repo has no `pytest.ini`, `pyproject.toml` or `setup.cfg`, so pytest uses its default discovery. It searches from the current directory for `test_*.py` / `*_test.py` files.
- Right now the only test file is `tests/test_smoke.py`. It has two tests.

## The two tests

1. **`test_openapi_document_can_be_loaded`** loads `docs/openapi.yaml` with `yaml.safe_load`. It then checks two things:
   - the `openapi` version starts with `3.`
   - the `paths` section is not empty

   This only checks that the API contract parses as YAML and has the basic structure. It does not validate the contract's content.
2. **`test_participant_files_are_present`** checks that these files exist:
   - `.claude/settings.json`
   - `.devcontainer/devcontainer.json`
   - `CLAUDE.md`
   - `Makefile`
   - `tracker/CR-2.md`
   - `tracker/README.md`

   If any are missing, the test fails and lists them in Latvian ("Trūkst faili: …").

## How it compares to the other targets

- **`make verify-setup`** checks the environment more broadly: the Python version, the packages (fastapi, pydantic v2, httpx, pytest, schemathesis), the `claude` CLI and `setup/claude-answer.md`. It then runs only the smoke test file.
- **`make lint-contract`** is the actual contract check. It runs `tools/lint_contract.py` on `docs/openapi.yaml`. Per `CLAUDE.md`, run it as well as `make test` whenever you change the contract.

If you add new tests under `tests/` (or anywhere pytest can find them), `make test` will pick them up automatically.
