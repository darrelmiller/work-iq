# workiq do-action

Execute a WorkIQ action via HTTP POST. Actions are named operations that perform a task rather than creating a stored resource, such as sending an email, copying a file, moving a message, or accepting a meeting invitation.

For global flags such as `--account`, `--endpoint`, headers, and log level, see `cli-commands.md`.

## Flags

| Flag | Required | Description |
|------|----------|-------------|
| `-u`, `--url <action-path>` | Yes | Action path, such as `/me/sendMail` or `/me/messages/{id}/copy`. |
| `-b`, `--body <json>` | No | JSON body with action parameters. Some actions require a body; others do not. |

## When to Use

- Sending an email immediately rather than creating a draft
- Accepting, declining, or tentatively accepting a meeting invitation
- Copying or moving a message to another folder
- Forwarding or replying to a message
- Subscribing to change notifications

Distinguish from `workiq create`: use `workiq do-action` for verbs such as send, copy, move, accept, decline, forward, or reply.

## Workflow

1. Use `workiq get-schema -u '<action-path>' -m post` when you are unsure of the required body.
2. Run `workiq do-action -u '<action-path>'` with `-b '<json>'` when needed.

## URL Format

Paths must start with `/` and must not include a scheme, authority, or version prefix:

- `https://example.com/v1.0/me/sendMail` ❌
- `/v1.0/me/sendMail` ❌
- `/me/sendMail` ✅

Put JSON bodies in single quotes in PowerShell.

## Examples

### Send an email immediately

```powershell
workiq do-action -u '/me/sendMail' -b '{"message":{"subject":"Hello","body":{"contentType":"Text","content":"Just checking in."},"toRecipients":[{"emailAddress":{"address":"colleague@example.com"}}]},"saveToSentItems":true}'
```

### Send a previously created draft

```powershell
workiq do-action -u '/me/messages/{id}/send'
```

### Copy a message to another folder

```powershell
workiq do-action -u '/me/messages/{id}/copy' -b '{"destinationId":"archive"}'
```

### Move a message to a folder

```powershell
workiq do-action -u '/me/messages/{id}/move' -b '{"destinationId":"inbox"}'
```

### Accept a meeting invitation

```powershell
workiq do-action -u '/me/events/{id}/accept' -b '{"comment":"See you there!","sendResponse":true}'
```

### Decline a meeting invitation

```powershell
workiq do-action -u '/me/events/{id}/decline' -b '{"comment":"Conflict — will catch up on recording.","sendResponse":true}'
```

### Forward a message

```powershell
workiq do-action -u '/me/messages/{id}/forward' -b '{"comment":"FYI","toRecipients":[{"emailAddress":{"address":"teammate@example.com"}}]}'
```

### Reply to a message

```powershell
workiq do-action -u '/me/messages/{id}/reply' -b '{"comment":"Thanks for the update!"}'
```
