# workiq create

Create a new WorkIQ entity by POSTing to a parent collection path. Use this to create calendar events, draft emails, tasks, Teams messages, and other M365 resources.

For global flags such as `--account`, `--endpoint`, headers, and log level, see `cli-commands.md`.

## Flags

| Flag | Required | Description |
|------|----------|-------------|
| `-u`, `--url <parent-path>` | Yes | Parent collection to POST to, such as `/me/events` or `/me/messages`. Do not include an item ID when creating a new entity. |
| `-b`, `--body <json>` | Yes | JSON body for the new entity. |

## When to Use

- Creating a new calendar event
- Creating a draft email; use `workiq do-action -u '/me/sendMail'` to send immediately
- Creating a new task in To Do or Planner
- Creating a new Teams channel message
- Any time you need to POST a new item to a collection

## Workflow

1. Run `workiq get-schema -u '<collection-path>' -m post` to confirm required fields.
2. Run `workiq create -u '<collection-path>' -b '<json>'` with a valid body.
3. Save the returned `id` if you need to reference or update the entity later.

## URL Format

Paths must start with `/` and must not include a scheme, authority, or version prefix:

- `https://example.com/v1.0/me/events` ❌
- `/v1.0/me/events` ❌
- `/me/events` ✅

URL-encode special characters in path segments. Put JSON bodies in single quotes in PowerShell so double quotes can be used inside the JSON.

## Examples

### Create a calendar event

```powershell
workiq create -u '/me/events' -b '{"subject":"Team Sync","start":{"dateTime":"2026-05-26T15:00:00","timeZone":"Pacific Standard Time"},"end":{"dateTime":"2026-05-26T15:30:00","timeZone":"Pacific Standard Time"},"attendees":[{"emailAddress":{"address":"colleague@example.com"},"type":"required"}]}'
```

### Create a draft email

```powershell
workiq create -u '/me/messages' -b '{"subject":"Project update","body":{"contentType":"HTML","content":"<p>Here is the latest update...</p>"},"toRecipients":[{"emailAddress":{"address":"manager@example.com"}}]}'
```

### Create a To Do task

```powershell
workiq create -u '/me/todo/lists/{listId}/tasks' -b '{"title":"Review pull request","dueDateTime":{"dateTime":"2026-06-05T17:00:00","timeZone":"UTC"}}'
```

### Create a Teams channel message

```powershell
workiq create -u '/teams/{teamId}/channels/{channelId}/messages' -b '{"body":{"content":"Deployment completed successfully."}}'
```
