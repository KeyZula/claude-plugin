---
name: keyzula
description: Use secrets from the user's KeyZula password manager (database passwords, API keys, tokens, 2FA codes) without putting them in the conversation. Use when a task needs a credential, an .env file or a config file with secrets.
---

# Using KeyZula secrets

The KeyZula MCP server gives you tools to find vault items and use their values without seeing them.

1. Find the item with `search_items` (by name, website or username). Each result lists `kz://` references
   such as `kz://Production/Postgres/password`. Use only references returned by `search_items`; never guess them.
2. To run a program that needs a secret, call `run_command` with the reference in `env`, for example
   `{"command": ["psql", "-c", "select 1"], "env": {"PGPASSWORD": "kz://Production/Postgres/password"}}`.
   The command runs without a shell; for pipes use `["sh", "-c", "…"]` and refer to the variable.
3. To create a config or `.env` file, call `inject_template` with `{{ kz://… }}` placeholders and an output path.
   Do not read the written file back or print it.
4. Only call `read_secret` when the user explicitly asks to see a value. The user approves each request on
   their computer; if it is not available or denied, say so and continue without the value.

Never print, echo, encode or otherwise transform a secret to get around output masking.
If a tool says the vault is locked or not logged in, ask the user to run `keyzula unlock`, or
`keyzula login` and `keyzula ssh-agent --daemon`, in their terminal.
