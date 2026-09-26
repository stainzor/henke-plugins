# Stack: Python (FastAPI, Flask, Django, scripts)

## Baseline commands
- Install from lockfile/requirements in a venv
- `ruff check .` / `flake8`, `mypy` or `pyright` if typed, `pytest -q`
- `pip-audit`, `bandit -r .`
- Django: `python manage.py check --deploy`, `makemigrations --check --dry-run`

## Specific checks
- Pydantic/serializer validation on all input; no raw SQL string formatting
- Django: `DEBUG=False`, `ALLOWED_HOSTS`, CSRF on, `SECURE_*` settings; permission classes on every view
- FastAPI: dependencies for auth on every router; response models do not leak fields
- `subprocess` with `shell=True` or user input; `pickle`/`yaml.load` on untrusted data
- Transactions (`atomic`) around multi-step writes; `select_for_update` for concurrent updates
- Background jobs (celery/cron/APScheduler): idempotent, retries, logging, alert on failure
- Timezones: aware datetimes, Europe/Stockholm conversion at the edges only
