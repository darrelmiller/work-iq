# workiq search-paths

Discover available WorkIQ paths by searching with a regex filter. Use this as the first step when you need structured entity commands but are not sure which path is available for a resource type.

For global flags such as `--account`, `--endpoint`, headers, and log level, see `cli-commands.md`.

## Flags

| Flag | Required | Description |
|------|----------|-------------|
| `-f`, `--filter <regex>` | No | Regex pattern to match available paths, such as `messages` or `.*calendar.*`. Omit to list all paths. |
| `-b`, `--backend <name>` | No | Backend to search: `graph-v1` (default), `sharepoint-rest`, or `dataverse`. |

## When to Use

- Before `workiq fetch`, `workiq create`, or another entity command when you are unsure of the exact path
- To discover what data is accessible for a concept such as calendar, messages, files, planner, or users
- To explore SharePoint REST or Dataverse paths

## Recommended Workflow

1. Run `workiq search-paths -f <regex>` with a broad filter to find candidate paths.
2. Run `workiq get-schema -u '<path>' -m <method>` to understand fields and parameters.
3. Run `workiq fetch` or the appropriate write/action command with the confirmed path.

## Regex Tips

- Filters are regex patterns, not simple wildcards.
- Use `messages` for a broad contains-style match.
- Use `.*calendar.*` when you want to be explicit about matching anywhere.
- Quote the filter in PowerShell when it contains `*`, `?`, `(`, or `)`.

## Examples

### Find all message-related paths

```powershell
workiq search-paths -f 'messages'
```

### Find calendar paths

```powershell
workiq search-paths -f '.*calendar.*'
```

### List all available paths

```powershell
workiq search-paths
```

### Search SharePoint REST paths

```powershell
workiq search-paths -b 'sharepoint-rest'
```

### Find Planner paths

```powershell
workiq search-paths -f 'planner'
```

### Find OneDrive/files paths

```powershell
workiq search-paths -f 'drive'
```
