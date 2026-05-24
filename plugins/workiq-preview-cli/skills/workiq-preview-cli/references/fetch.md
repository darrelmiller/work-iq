# workiq fetch

Fetch one or more WorkIQ entities by path using HTTP GET. Use this for precise, structured retrieval of M365 data when `workiq ask` is not specific enough: lists with selected fields, filtered results, or a single entity by ID.

For global flags such as `--account`, `--endpoint`, headers, and log level, see `cli-commands.md`.

## Flags

| Flag | Required | Description |
|------|----------|-------------|
| `-u`, `--urls <path>` | Yes | Entity path to fetch, such as `/me` or `/me/messages?$top=10`. Repeat for multiple paths. |

## When to Use

- When you need a structured list of entities: messages, events, files, tasks, and similar resources
- When you need OData filters or selected fields
- When you already have an entity ID and want its details
- For multi-fetch: pass `-u` more than once to retrieve several entities in one command

Prefer `workiq ask --json` for open-ended questions. Use `workiq fetch` when you need precise, filtered, or structured data.

## URL Format

Paths must:

- Start with `/` as a WorkIQ path
- **Not** include a scheme or authority: `https://example.com/v1.0/me/messages` ❌, `/me/messages` ✅
- **Not** include a version prefix: `/v1.0/me/messages` ❌, `/me/messages` ✅
- Have query parameter values URL-encoded

Common URL encodings for OData query values:

| Character | Encoded | Example |
|-----------|---------|---------|
| Space | `%20` | `$filter=isRead%20eq%20false` |
| Single quote `'` | `%27` | `$filter=subject%20eq%20%27Hello%27` |
| `(` | `%28` | `$filter=startsWith%28subject%2C%27Re%3A%27%29` |
| `)` | `%29` | Same as above |
| `:` | `%3A` | In string literals |
| `/` in values | `%2F` | `$orderby=start%2FdateTime` |
| `,` in values | `%2C` | In string literals |

> **Tip:** OData keywords and property names such as `$filter=`, `isRead`, and `eq` do not need encoding. Encode values and string literals inside query parameters.

## OData Query Tips

| Parameter | Purpose | Example |
|-----------|---------|---------|
| `$top` | Limit result count | `$top=10` |
| `$filter` | Filter results | `$filter=isRead%20eq%20false` |
| `$select` | Return only specified fields | `$select=subject,from,receivedDateTime` |
| `$orderby` | Sort results | `$orderby=receivedDateTime%20desc` |
| `$expand` | Include related entities inline | `$expand=attachments` |

In PowerShell, put URLs in single quotes so `$top`, `$filter`, `?`, and `&` are passed literally.

## Examples

### Get the signed-in user's profile

```powershell
workiq fetch -u '/me'
```

### Get unread emails, top 10

```powershell
workiq fetch -u '/me/messages?$top=10&$filter=isRead%20eq%20false&$select=subject,from,receivedDateTime'
```

### Get upcoming calendar events

```powershell
workiq fetch -u '/me/events?$top=5&$orderby=start%2FdateTime&$select=subject,start,end,location'
```

### Get a specific message by ID

```powershell
workiq fetch -u '/me/messages/{id}'
```

### Fetch multiple entities in one command

```powershell
workiq fetch -u '/me' -u '/me/mailFolders/inbox'
```

### Get files from OneDrive

```powershell
workiq fetch -u '/me/drive/root/children?$select=name,size,lastModifiedDateTime'
```

### Get Teams channels for a group

```powershell
workiq fetch -u '/teams/{teamId}/channels'
```
