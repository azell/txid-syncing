```bash
# Install the Postgres libs
brew install libpq || true
uv init --bare

# Compile the Postgres client
PATH="/opt/homebrew/opt/libpq/bin:$PATH" \
LDFLAGS="-L$(brew --prefix openssl@3)/lib" \ 
CPPFLAGS="-I$(brew --prefix openssl@3)/include" \ 
uv add -r requirements.txt

# Finish migration
rm -rf .venv venv
uv sync
```
