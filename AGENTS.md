# FastAPI Best Architecture (FBA) - Agent Guide

## Quick Start

```bash
# Install dependencies (requires uv)
uv sync

# Initialize project (creates .env, installs deps, creates DB)
fba init --auto

# Run server (hot reload enabled by default)
fba run

# Run tests
cd backend && pytest
```

## Project Structure

```
backend/
├── app/           # Application modules (api, schema, service, crud, model)
│   ├── admin/     # Admin module
│   └── task/      # Task module (Celery)
├── common/        # Shared: enums, exceptions, security, cache, response
├── core/          # Config (conf.py), registrar, path config
├── database/      # DB (SQLAlchemy) + Redis
├── middleware/    # Custom middleware
├── plugin/        # Plugin system (code_generator, config, dict, email, notice, oauth2)
└── utils/         # Utilities
```

## Architecture (Three-tier, not Django-style)

| Layer | Java equivalent | FBA directory |
|-------|-----------------|---------------|
| view/controller | controller | `app/*/api/` |
| dto | dto | `app/*/schema/` |
| service + impl | service + impl | `app/*/service/` |
| dao/mapper | dao/mapper | `app/*/crud/` |
| model/entity | model/entity | `app/*/model/` |

## CLI Commands (`fba`)

| Command | Description |
|---------|-------------|
| `fba init` | Interactive init (creates .env, installs deps, inits DB tables) |
| `fba init --auto` | Non-interactive init |
| `fba run [host] [port]` | Run API server (default 127.0.0.1:8000) |
| `fba run --no-reload --workers N` | Production mode (no reload, multi-worker) |
| `fba add <path or repo>` | Add plugin (ZIP or Git) |
| `fba add -f <repo>` | Add plugin with frontend |
| `fba remove <name>` | Remove plugin |
| `fba format` | Format code (uses prek/ruff) |
| `fba celery worker` | Start Celery worker |
| `fba celery beat` | Start Celery beat |
| `fba celery flower` | Start Celery flower (port 8555) |
| `fba import -tc <schema> -tn <table>` | Import table for code gen |
| `fba codegen` | Generate code from imported tables |
| `fba migration` | Alembic subcommands (upgrade, downgrade, etc.) |

## Linting & Formatting

- **Linter**: Ruff (line-length 120, single quotes)
- **Format check**: `ruff check` / `ruff format`
- **Pre-commit hooks**: Run `pre-commit install` to enable
- **Config**: pyproject.toml `[tool.ruff]` section
- **Code format**: `fba format` or `prek run --all-files`

## Testing

- **Test location**: `backend/app/admin/tests/`
- **Fixtures**: `conftest.py` provides `client` and `token_headers`
- **Run**: `cd backend && pytest`
- **Test DB**: Uses TestClient with dependency override (not real DB)

## Database

- **ORM**: SQLAlchemy 2.0 (async)
- **Migrations**: Alembic (location: `backend/alembic/`)
- **Supported**: PostgreSQL (default), MySQL
- **Config**: `.env` - `DATABASE_TYPE`, `DATABASE_HOST`, etc.

## Plugins

- Built-in: code_generator, config, dict, email, notice, oauth2
- Each plugin has its own README in `backend/plugin/<name>/`
- Plugin SQL scripts auto-run on install (use `--no-sql` to disable)

## Key Files

- `backend/main.py` - FastAPI app entry (via plugin loader)
- `backend/run.py` - Granian server for IDE debug
- `backend/cli.py` - All `fba` commands
- `backend/core/conf.py` - Settings (pydantic-settings)
- `backend/.env.example` - Environment template

## Common Pitfalls

1. **Running from wrong directory**: CLI expects to run from `backend/` or project root
2. **Missing .env**: Run `fba init` first to generate it
3. **DB not created**: `fba init` creates DB but you must ensure DB server is running
4. **Plugin dependency install**: `main.py` auto-installs plugin deps on startup
5. **Format rule exceptions**: `model/*.py` ignores TC003; specific files have custom ignores (see pyproject.toml)

## Documentation

- Official docs: https://fastapi-practices.github.io/fastapi_best_architecture_docs/
- Chinese docs: Available in project