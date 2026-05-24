# workiq fetch-blob

Download binary content from a WorkIQ path. Use this for file content, email attachments, document downloads, profile photos, and other binary resources from M365.

For global flags such as `--account`, `--endpoint`, headers, and log level, see `cli-commands.md`.

## Flags

| Flag | Required | Description |
|------|----------|-------------|
| `-u`, `--url <path>` | Yes | Single path pointing to binary content, such as `/me/photo/$value` or `/me/drive/items/{id}/content`. |
| `-o`, `--output <dir>` | No | Output directory. Defaults to the current directory. |

## When to Use

- Downloading a file from OneDrive or SharePoint
- Retrieving an email attachment
- Downloading a profile photo or exported binary content

Distinguish from `workiq fetch`: use `workiq fetch-blob` when the path returns bytes. Use `workiq fetch` when the path returns JSON.

## URL Format

Paths must:

- Start with `/` as a WorkIQ path
- **Not** include a scheme or authority: `https://example.com/v1.0/me/photo/$value` ❌, `/me/photo/$value` ✅
- **Not** include a version prefix: `/v1.0/me/photo/$value` ❌, `/me/photo/$value` ✅

In PowerShell, always quote paths containing `$value` with single quotes so `$value` is not treated as a variable.

## Path Conventions

| Resource | Path pattern |
|----------|--------------|
| Profile photo | `/me/photo/$value` |
| OneDrive file content | `/me/drive/items/{id}/content` |
| SharePoint file content | `/drives/{driveId}/items/{id}/content` |
| Email attachment bytes | `/me/messages/{id}/attachments/{attachmentId}/$value` |

## Workflow

1. Use `workiq fetch` to list items and retrieve IDs, for example `/me/drive/root/children`.
2. Use `workiq fetch-blob` with the content path to download the binary data.
3. Use `-o` when you need the file in a specific directory.

## Examples

### Download your profile photo

```powershell
workiq fetch-blob -u '/me/photo/$value' -o '.\downloads'
```

### Download a file from OneDrive by item ID

```powershell
workiq fetch-blob -u '/me/drive/items/{id}/content' -o '.\downloads'
```

### Download an email attachment

```powershell
workiq fetch-blob -u '/me/messages/{messageId}/attachments/{attachmentId}/$value' -o '.\attachments'
```

### Download a file from a shared drive

```powershell
workiq fetch-blob -u '/drives/{driveId}/items/{itemId}/content'
```
