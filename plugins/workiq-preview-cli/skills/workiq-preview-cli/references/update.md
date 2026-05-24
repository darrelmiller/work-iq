# workiq update

Update an existing WorkIQ entity via HTTP PATCH. Only fields included in the body are changed; omitted fields are left untouched.

For global flags such as `--account`, `--endpoint`, headers, and log level, see `cli-commands.md`.

## Flags

| Flag | Required | Description |
|------|----------|-------------|
| `-u`, `--url <entity-path>` | Yes | Entity path to update, such as `/me/events/{id}`. |
| `-b`, `--body <json>` | Yes | JSON body containing only the fields to update. Uses PATCH semantics. |

## When to Use

- Marking an email as read or unread
- Updating the subject, time, or location of a calendar event
- Changing the status or due date of a task
- Updating a document's metadata
- Any partial update to an existing M365 entity

## Workflow

1. Obtain the entity `id` from `workiq fetch` or `workiq create`.
2. Optionally run `workiq get-schema -u '<entity-path>' -m patch` to confirm which fields are updatable.
3. Run `workiq update` with only the fields you want to change.

## URL Format

Paths must start with `/` and must not include a scheme, authority, or version prefix:

- `https://example.com/v1.0/me/events/{id}` ❌
- `/v1.0/me/events/{id}` ❌
- `/me/events/{id}` ✅

URL-encode special characters in path segments. Put JSON bodies in single quotes in PowerShell.

## Examples

### Mark a message as read

```powershell
workiq update -u '/me/messages/{id}' -b '{"isRead":true}'
```

### Update a calendar event's subject and location

```powershell
workiq update -u '/me/events/{id}' -b '{"subject":"Updated: Team Sync","location":{"displayName":"Conference Room B"}}'
```

### Update a To Do task's due date

```powershell
workiq update -u '/me/todo/lists/{listId}/tasks/{taskId}' -b '{"dueDateTime":{"dateTime":"2026-06-10T17:00:00","timeZone":"UTC"}}'
```

### Mark a task as complete

```powershell
workiq update -u '/me/todo/lists/{listId}/tasks/{taskId}' -b '{"status":"completed"}'
```

### Move a message to a different category

```powershell
workiq update -u '/me/messages/{id}' -b '{"categories":["Project Alpha"]}'
```
