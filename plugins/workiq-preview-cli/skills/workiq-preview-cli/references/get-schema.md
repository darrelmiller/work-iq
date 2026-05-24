# workiq get-schema

Retrieve the OpenAPI schema for a WorkIQ path or operation. Use this to understand available fields, query parameters, and body shapes for create, update, action, and delete commands.

For global flags such as `--account`, `--endpoint`, headers, and log level, see `cli-commands.md`.

## Flags

| Flag | Required | Description |
|------|----------|-------------|
| `-o`, `--operation-ids <ids>` | No* | Operation ID or IDs, such as `me.CreateMessages`. Use this only when you do not know the path. |
| `-u`, `--url <path>` | No* | Entity path, such as `/me/messages`. Preferred when you have a path from `workiq fetch` or `workiq search-paths`. |
| `-m`, `--method <method>` | No | HTTP method filter: `get`, `post`, `patch`, or `delete`. |
| `-v`, `--api-version <ver>` | No | `v1.0` (default) or `beta`. Use `beta` for preview features. |

*Use either `--url` or `--operation-ids`.

## When to Use

- Before `workiq create` or `workiq update` to understand the required body shape
- Before `workiq do-action` to learn action parameters
- When `workiq fetch` returns unfamiliar fields
- When you need to know which OData query parameters are supported
- To check whether `beta` exposes fields not available in `v1.0`

## URL Format

Paths must start with `/` and must not include a scheme, authority, or version prefix:

- `https://example.com/v1.0/me/messages` ❌
- `/v1.0/me/messages` ❌
- `/me/messages` ✅

## Examples

### Get the schema for reading messages

```powershell
workiq get-schema -u '/me/messages' -m get
```

### Get the schema for creating a calendar event

```powershell
workiq get-schema -u '/me/events' -m post
```

### Get the schema for updating a message

```powershell
workiq get-schema -u '/me/messages/{id}' -m patch
```

### Get a beta schema

```powershell
workiq get-schema -u '/me/messages' -m get -v beta
```

### Look up by operation ID

```powershell
workiq get-schema -o 'me.CreateMessages'
```
