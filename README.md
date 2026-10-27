# KeyZula for Claude

Let Claude use the passwords, API keys and tokens in your [KeyZula](https://keyzula.com) vault without
the values ever entering the conversation. Claude finds items by name, website or username and passes
secrets to the programs it runs through `kz://` references.

## Install

In Claude Code:

```
/plugin install keyzula --marketplace KeyZula/claude-plugin
```

(On older versions: `/plugin marketplace add KeyZula/claude-plugin`, then `/plugin install keyzula@keyzula`.)
For Claude Desktop without plugins, download the extension from [keyzula.com/en/ai](https://keyzula.com/en/ai/#setup).

## What it does

The plugin adds the KeyZula MCP server and a skill that explains how to use it:

| Tool | What it does | What Claude receives |
|---|---|---|
| `search_items` | Searches your vault | Names, locations, usernames, websites and `kz://` references, never values |
| `run_command` | Runs a command with secrets in its environment variables | Exit code and output, with secret values concealed |
| `inject_template` | Fills a `{{ kz://… }}` template and writes a file only you can read (0600) | Only the file path |
| `generate_password` | Generates a password or passphrase | The generated value (not saved) |
| `vault_status` | Shows sign-in and lock status | Status |
| `read_secret` | Shows one value, such as a 2FA code | Off by default; when turned on, every request needs your approval in a KeyZula window |

## Requirements

1. Node.js 20 or later (the server runs with `npx @keyzula/cli`).
2. A KeyZula account (free) and the KeyZula command-line tool, signed in and unlocked once in a terminal:

```sh
npm i -g @keyzula/cli        # or: brew install keyzula/tap/keyzula
keyzula login
keyzula ssh-agent --daemon
```

The MCP server reads only from this unlocked agent and never asks for your master password.

## Settings

- **Allow showing values**: offers `read_secret`. Each request opens an approval window on your computer.
- **Ask before every use**: also asks before each command or file that uses a secret.
- **Service account token** (optional): limits Claude to the collections of an organization's service account.

## What runs and where data goes

- The plugin starts `npx -y @keyzula/cli@<version> mcp`, which downloads the KeyZula CLI from npm.
- The server talks to the KeyZula agent on your computer over a private local socket. The agent keeps your
  vault in sync with your KeyZula server (`https://app.keyzula.com` by default). Vault data is end-to-end
  encrypted; the server never sees it in plain text.
- When Claude uses an item, an access event (item ID, action and time, never the name or value) is sent
  to your KeyZula server and shown in your access log as "Used by an AI assistant".
- Nothing is sent to Anthropic or any other third party by the plugin itself.

More: [keyzula.com/en/ai](https://keyzula.com/en/ai/) · [Privacy policy](https://keyzula.com/en/privacy/) ·
[Support](https://keyzula.com/en/support/)

## License

The files in this repository are under the MIT License. The KeyZula CLI and service have their own terms:
[keyzula.com/en/terms](https://keyzula.com/en/terms/).
