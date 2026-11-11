---
name: gws
description: Agent Skill for Google Workspace CLI. Use when interacting with user's Gmail, Calendar, Drive, Sheets, Docs, Tasks, etc. Google Services.
---

# gws — Google Workspace CLI

## Prerequisites

The `gws` binary must be on `$PATH`.

### Multi-accounts management & Authentication

Credentials are isolated per account alias under `~/.config/gws/<alias>/`, with `~/.config/gws/accounts.json` serving as the account registry:

```json
{
  "main": {
    "description": "Main Google account"
  }
}
```

Before running any authenticated `gws` command:

1. Read `~/.config/gws/accounts.json` to discover available account aliases and their descriptions.
2. If only one account is registered, or the user's intent clearly matches one account, use that `<alias>`. If multiple accounts exist and the target account is ambiguous, ask the user which account to use.
3. Set `GOOGLE_WORKSPACE_CLI_KEYRING_BACKEND=file` and `GOOGLE_WORKSPACE_CLI_CONFIG_DIR="$HOME/.config/gws/<alias>"` on every authenticated `gws` command.

### Check authentication status (also useful for inspecting the `user` email of an alias)

```bash
GOOGLE_WORKSPACE_CLI_KEYRING_BACKEND=file GOOGLE_WORKSPACE_CLI_CONFIG_DIR="$HOME/.config/gws/<alias>" gws auth status
```

```powershell
$env:GOOGLE_WORKSPACE_CLI_KEYRING_BACKEND="file"; $env:GOOGLE_WORKSPACE_CLI_CONFIG_DIR="$HOME/.config/gws/<alias>"; gws auth status
```

Do not paste the full auth status output into chat due to security concerns. (E.g. pipe it with `jq`, or redirect the output to a file, etc.)

### To log in or add a new account `<alias>`

1. Create `~/.config/gws/<alias>` (`chmod 700`), copy `~/.config/gws/client_secret.json` into `~/.config/gws/<alias>/client_secret.json`, and add `<alias>` to `~/.config/gws/accounts.json`.
2. Tell the user to run:

```bash
GOOGLE_WORKSPACE_CLI_KEYRING_BACKEND=file GOOGLE_WORKSPACE_CLI_CONFIG_DIR="$HOME/.config/gws/<alias>" gws auth login
```

```powershell
$env:GOOGLE_WORKSPACE_CLI_KEYRING_BACKEND="file"; $env:GOOGLE_WORKSPACE_CLI_CONFIG_DIR="$HOME/.config/gws/<alias>"; gws auth login
```

Besides OAuth2, Service-account authentication is also supported when the user intentionally chooses it:

```bash
export GOOGLE_APPLICATION_CREDENTIALS=/path/to/key.json
```

```powershell
$env:GOOGLE_APPLICATION_CREDENTIALS="/path/to/key.json"
```

## Global Command Discovery

Start with help output instead of assuming exact command shapes:

```bash
gws --help
gws <service> --help
gws <service> <resource> --help
gws <service> <helper-or-resource> --help
```

Use schemas before calling raw API methods:

```bash
gws schema <service.resource.method>
gws schema <service.resource.method> --resolve-refs
```

Use `gws --help` to see the current list of supported services. Examples may include Gmail, Calendar, Drive, Sheets, Docs, Slides, Tasks, Keep, etc.

## CLI Syntax

```bash
gws <service> <resource> [sub-resource] <method> [flags]
gws schema <service.resource.method> [--resolve-refs]
```

Examples:

```bash
gws drive files list --params '{"pageSize": 10}'
gws drive files get --params '{"fileId": "abc123"}'
gws sheets spreadsheets get --params '{"spreadsheetId": "..."}'
gws gmail users messages list --params '{"userId": "me"}'
gws schema drive.files.list
```

## Global Flags

<!-- markdownlint-disable MD013 -->

| Flag                    | Description                                              |
| ----------------------- | -------------------------------------------------------- |
| `--format <FORMAT>`     | Output format: `json` (default), `table`, `yaml`, `csv`  |
| `--dry-run`             | Validate locally without calling the API, when supported |
| `--sanitize <TEMPLATE>` | Screen responses through Model Armor                     |

## Method Flags

<!-- markdownlint-disable MD013 -->

| Flag                           | Description                                                       |
| ------------------------------ | ----------------------------------------------------------------- |
| `--params '{"key": "val"}'`    | URL/query parameters                                              |
| `--json '{"key": "val"}'`      | Request body                                                      |
| `-o, --output <PATH>`          | Save binary responses to file                                     |
| `--upload <PATH>`              | Upload file content as multipart media                            |
| `--upload-content-type <MIME>` | MIME type for uploaded content; otherwise inferred from extension |
| `--page-all`                   | Auto-paginate, one JSON page per line as NDJSON                   |
| `--page-limit <N>`             | Max pages when using `--page-all`                                 |
| `--page-delay <MS>`            | Delay between pages in milliseconds                               |

## Safety Rules

- Never output secrets, API keys, OAuth client secrets, tokens, encrypted credentials, or auth exports.
- Confirm with the user before executing any commands that is not read-only. Examples are writing emails, creating calendar events, uploading files, or deleting resources.
- Prefer `--dry-run` before destructive or externally visible operations when the command supports it.
- For read operations that may expose private emails, calendar details, files, contacts, or other PII, confirm with the user before proceeding.
- Use `--sanitize` when the user asks for Model Armor screening or when content safety/PII screening is required.

## Shell Tips

- JSON flags: wrap `--params` and `--json` values in single quotes so the shell does not interpret inner double quotes.

  ```bash
  gws drive files list --params '{"pageSize": 5}'
  ```

- zsh `!` expansion: Sheet ranges like `Sheet1!A1` contain `!`, which zsh may interpret as history expansion. Use double quotes with escaped inner quotes instead of single quotes.

  ```bash
  # WRONG in zsh
  gws sheets +read --spreadsheet ID --range 'Sheet1!A1:D10'

  # CORRECT
  gws sheets +read --spreadsheet ID --range "Sheet1!A1:D10"
  ```
