# workiq upload-blob

Upload a local file to a WorkIQ path via HTTP PUT. Use this to upload files to OneDrive or SharePoint, or to replace the content of an existing file.

For global flags such as `--account`, `--endpoint`, headers, and log level, see `cli-commands.md`.

## Flags

| Flag | Required | Description |
|------|----------|-------------|
| `-u`, `--url <target-path>` | Yes | Target path for the upload, such as `/me/drive/root:/file.txt:/content`. |
| `-f`, `--file <local-path>` | Yes | Local file to upload. |

## When to Use

- Uploading a file to OneDrive
- Uploading a file to a SharePoint document library
- Replacing the content of an existing file

## URL Format

Paths must start with `/` and must not include a scheme, authority, or version prefix:

- `https://example.com/v1.0/me/drive/root:/file.txt:/content` ❌
- `/v1.0/me/drive/root:/file.txt:/content` ❌
- `/me/drive/root:/file.txt:/content` ✅

Use the path-based format `root:/{path}:/content` and include the leading `/` before the filename.

## Path Conventions

| Action | Path pattern |
|--------|--------------|
| Upload to OneDrive root by filename | `/me/drive/root:/{filename}:/content` |
| Upload to a specific folder | `/me/drive/root:/{folder}/{filename}:/content` |
| Replace a file by item ID | `/me/drive/items/{id}/content` |
| Upload to SharePoint drive | `/drives/{driveId}/root:/{filename}:/content` |

## Gotchas

- Simple PUT uploads are best for small files. Large files may require an upload-session workflow.
- Quote paths in PowerShell when they contain spaces or special characters.
- If the target filename contains characters that are not valid in a URL path, encode them.

## Examples

### Upload a file to OneDrive root

```powershell
workiq upload-blob -u '/me/drive/root:/report.pdf:/content' -f 'C:\Users\user\Documents\report.pdf'
```

### Upload a file to a subfolder in OneDrive

```powershell
workiq upload-blob -u '/me/drive/root:/Projects/Alpha/spec.docx:/content' -f 'C:\Users\user\Documents\spec.docx'
```

### Replace an existing file by ID

```powershell
workiq upload-blob -u '/me/drive/items/{id}/content' -f 'C:\Users\user\Documents\updated-report.pdf'
```
