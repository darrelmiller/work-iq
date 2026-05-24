# WorkIQ CLI setup commands (auth, config, version)

These are the one-time / out-of-band commands you run via the shell to set up or maintain WorkIQ. They are distinct from the data commands (`workiq ask`, `workiq fetch`, etc.) documented in the other reference files.

## Prerequisite

The `workiq` binary must be on `PATH`. If `workiq version` fails, see [install-prerequisites.md](install-prerequisites.md).

## Command reference

| Task | Command |
|---|---|
| Show installed version | `workiq version` |
| Sign in (opens browser, sets default account) | `workiq auth login` |
| Sign in with a specific account | `workiq auth login --account user@contoso.com` |
| Sign out / clear cached tokens | `workiq auth logout` |
| Grant additional permission scopes | `workiq auth consent --scopes Mail.ReadWrite Calendars.ReadWrite` |
| Show current persisted defaults | `workiq config show` |
| Set a default option | `workiq config set <key>=<value>` |
| Remove a config key | `workiq config unset <key>` |
| Wipe all defaults (deletes `.workiq.json`) | `workiq config reset` |
| Accept the EULA non-interactively | `workiq accept-eula` |
| Generate a debug share link for a conversation | `workiq debug <conversationId>` |
| Generate a share link for a conversation | `workiq share <conversationId>` |
| List available WorkIQ agents | `workiq agents list` |
| Get an agent card by id | `workiq agents get-card --agent-id <id>` |

## Global flags (available on every command)

| Flag | Purpose |
|---|---|
| `-e`, `--endpoint <url>` | Override the A2A endpoint (default: `https://workiq.svc.cloud.microsoft/a2a/`) |
| `-H`, `--header 'Key: Value'` | Add a custom HTTP header (repeatable) |
| `--account <email>` | Use a specific cached account (instead of the default) |
| `-l`, `--log-level <level>` | One of `Trace`, `Debug`, `Information`, `Warning`, `Error`, `Critical`, `None` |
| `--version` | Show version information |
| `-?`, `-h`, `--help` | Show help for the (sub)command |

## When to use these

- A data command returns **HTTP 403 Forbidden** → run `workiq auth consent --scopes <scopes>` with the scopes needed for that path, then retry.
- Sign-in is broken or hangs on Windows → `workiq config set disableBrokeredAuth=true` to force browser-based login.
- You suspect a **stale token** or the wrong cached account → `workiq auth logout`, then `workiq auth login` (optionally with `--account`).
- You want to verify the installed binary version → `workiq version`.

## Examples

### First-time setup on a new machine

```powershell
npm install -g @microsoft/workiq@preview
workiq accept-eula
workiq auth login
workiq version
```

### Switching to a different signed-in account

```powershell
workiq auth login --account someone-else@contoso.com
workiq config set account=someone-else@contoso.com
```

### Granting additional scopes after a 403

```powershell
workiq auth consent --scopes Mail.ReadWrite Calendars.ReadWrite Files.ReadWrite.All
```
