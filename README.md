# scafolding

> A minimal Python CI scaffold — Makefile, pytest with coverage, pylint, and a GitHub Actions workflow that runs all three on every push.

The point isn't `add(1, 2)`. It's the surrounding machinery: a reproducible
`make install / lint / test` loop that a CI runner executes with the exact same
commands you run locally. Copy this as the starting skeleton for a new Python
project.

## Use it

```bash
make install    # upgrade pip, install requirements
make lint       # pylint (R and C categories disabled)
make test       # pytest -vv with coverage on hello.py
```

## CI

`.github/workflows/python-test.yml` runs on **every push**:

1. Checkout
2. Set up Python 3.11.9
3. `make install`
4. `make lint`
5. `make test`

Because CI calls the same Make targets you do, there's no drift between "works
locally" and "passes CI" — fix the Makefile and both move together.

## Files

```
hello.py                          # The module under test — add(x1, x2)
test_hello.py                     # pytest test
Makefile                          # install · lint · test
requirements.txt                  # pylint, pytest-cov
.github/workflows/python-test.yml # CI pipeline
```

## Adapting it

- **New module** — add it, then extend the `lint` and `test` targets to cover it
  (`--cov=<module>`).
- **Stricter linting** — `make lint` currently passes `--disable=R,C`, which
  silences refactor and convention warnings. Remove them for a full pylint run.
- **Python version** — change it in the workflow's `setup-python` step.

## Known issues

- **`requirements.txt` lists `pylist`**, which is a typo for `pylint` (already
  listed on the next line). `pip install -r requirements.txt` will fail or
  install an unrelated package, which takes CI down with it.
- **`pytest` isn't listed** — it only arrives transitively via `pytest-cov`.
  Worth pinning explicitly.
- **`hello.py` runs `print(add(1, 2))` at import time**, so the test run prints
  stray output. Guard it with `if __name__ == "__main__":`.
- The workflow is named "Azure Python 3.11.9" but doesn't touch Azure.
- `actions/checkout@v2` and `actions/setup-python@v1` are several major versions
  behind.
