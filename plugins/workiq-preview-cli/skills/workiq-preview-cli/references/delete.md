# workiq delete

Delete a WorkIQ entity via HTTP DELETE. This is a destructive operation; use with care, especially for emails and calendar events.

For global flags such as `--account`, `--endpoint`, headers, and log level, see `cli-commands.md`.

## Flags

| Flag | Required | Description |
|------|----------|-------------|
| `-u`, `--url <entity-path>` | Yes | Entity path including the item ID, such as `/me/events/{id}`. |

## When to Use

- Deleting a calendar event
- Deleting a draft email
- Removing a task from To Do or Planner
- Deleting a Teams message where permitted

## Gotchas

- Email deletion usually moves the message to Deleted Items; it may not permanently delete it.
- Calendar event deletion removes it from the calendar and may send cancellation notices to attendees if it was an organized meeting.
- Always confirm the entity ID with `workiq fetch` before deleting.

## Workflow

1. Use `workiq fetch` to confirm you have the correct entity and ID.
2. Run `workiq delete -u '<entity-path>'` with the full path including ID.

## URL Format

Paths must start with `/` and must not include a scheme, authority, or version prefix:

- `https://example.com/v1.0/me/events/{id}` ❌
- `/v1.0/me/events/{id}` ❌
- `/me/events/{id}` ✅

## Examples

### Delete a calendar event

```powershell
workiq delete -u '/me/events/{id}'
```

### Delete a draft email

```powershell
workiq delete -u '/me/messages/{id}'
```

### Delete a To Do task

```powershell
workiq delete -u '/me/todo/lists/{listId}/tasks/{taskId}'
```

### Delete a Planner task

```powershell
workiq delete -u '/planner/tasks/{taskId}'
```
