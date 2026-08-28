How the project was migrated to uv:
```bash
# Install the Postgres libs
brew install libpq
uv init --bare

# Compile the Postgres client
PATH="/opt/homebrew/opt/libpq/bin:$PATH" LDFLAGS="-L$(brew --prefix openssl@3)/lib" CPPFLAGS="-I$(brew --prefix openssl@3)/include" \
  uv add -r requirements.txt

# Finish migration
rm -rf .venv venv
uv sync
```

Remove the Postgres volume if needed:
```bash
docker compose down -v
```

Spin up the Postgres service:
```bash
docker compose up --detach --remove-orphans --wait
```

Load the schema:
```bash
docker compose exec postgres psql -U postgres -f /schema/apply-schema.sql sync-test
```

Start the API server:
```bash
uv run --env-file .env src/demo-app.py
```

Start the sync:
```bash
uv run src/sqlite-via-api-client.py
```

Start the load generator:
```bash
uv run --env-file .env src/load-generator.py
```

Run the validator during and after the load generator run:
```bash
uv run --env-file .env src/consistency-check.py sqlite
```
