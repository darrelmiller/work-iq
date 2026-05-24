# workiq call-function

Call a WorkIQ function via HTTP GET. Functions are named operations that return computed results rather than stored entities, such as change tracking, upcoming reminders, or free/busy availability.

For global flags such as `--account`, `--endpoint`, headers, and log level, see `cli-commands.md`.

## Flags

| Flag | Required | Description |
|------|----------|-------------|
| `-u`, `--url <function-path>` | Yes | Function path with inline parameters when required, such as `/me/calendar/getSchedule` or `/me/reminderView(startDateTime='...',endDateTime='...')`. |

## When to Use

- When you need free/busy availability for scheduling (`getSchedule`)
- When you need upcoming meeting reminders (`reminderView`)
- When a WorkIQ path uses function-call syntax such as `functionName(param=value)`

Distinguish from `workiq fetch`: use `workiq call-function` when the path includes a function invocation or named function. Use `workiq fetch` for plain collection or item paths.

## URL Format

Paths must start with `/` and must not include a scheme, authority, or version prefix:

- `https://example.com/v1.0/me/reminderView(...)` ❌
- `/v1.0/me/reminderView(...)` ❌
- `/me/reminderView(...)` ✅

URL-encode special characters in inline parameter values. In PowerShell, quote the full function path with single quotes.

## OData and Inline Parameter Tips

| Character | Encoded | Notes |
|-----------|---------|-------|
| Space | `%20` | Encode inside values |
| Single quote `'` | `%27` | Often needed around string values |
| `:` | `%3A` | Common in timestamps |
| `,` | `%2C` | Encode when it is part of a value, not a function separator |

## Examples

### Get upcoming meeting reminders

```powershell
workiq call-function -u '/me/reminderView(startDateTime=%272026-06-01T00%3A00%3A00Z%27,endDateTime=%272026-06-30T23%3A59%3A59Z%27)'
```

### Call a schedule function path

```powershell
workiq call-function -u '/me/calendar/getSchedule'
```

### Calendar view delta

```powershell
workiq call-function -u '/me/calendarView/delta?startDateTime=2026-06-01T00%3A00%3A00Z&endDateTime=2026-06-07T23%3A59%3A59Z'
```
