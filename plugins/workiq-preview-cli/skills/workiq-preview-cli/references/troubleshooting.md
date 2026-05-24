# Troubleshooting WorkIQ CLI

Use this reference when a `workiq` shell command fails, exits with a non-zero status code, writes an error to stderr, or returns output that is hard to parse.

## `workiq` command not found

**Symptom:** PowerShell prints `'workiq' is not recognized as the name of a cmdlet` or another shell prints `workiq: command not found`.

**Cause:** The WorkIQ CLI is not installed or is not on `PATH`.

**Fix:** Install the CLI with npm, then open a new shell. See `install-prerequisites.md`.

```powershell
npm install -g @microsoft/workiq@preview
workiq --help
```

## `workiq auth login` hangs on Windows

**Symptom:** `workiq auth login` opens no browser, hangs, or never completes.

**Cause:** Brokered authentication can hang in some Windows environments before browser fallback completes.

**Fix:** Disable brokered auth, then retry login.

```powershell
workiq config set disableBrokeredAuth=true
workiq auth login
```

## HTTP 401 or not signed in

**Symptom:** A command returns `HTTP 401`, `Unauthorized`, `not signed in`, or asks for authentication.

**Cause:** The current account has no valid WorkIQ sign-in session.

**Fix:** Sign in, then retry the original command.

```powershell
workiq auth login
```

## HTTP 403 or insufficient scopes

**Symptom:** A command returns `HTTP 403 Forbidden`, `insufficient scopes`, or a permission error for a path such as `/me/events` or `/me/messages`.

**Cause:** The current account has not consented to the scopes required for that path or operation.

**Fix:** Grant the needed scopes, then retry.

```powershell
workiq auth consent --scopes Calendars.Read
workiq auth consent --scopes Mail.Read Calendars.ReadWrite Sites.Read.All
```

## HTTP 400 on a path

**Symptom:** `workiq fetch`, `workiq create`, `workiq update`, `workiq do-action`, or another path command returns `HTTP 400`, parser errors, or validation errors.

**Cause:** The path format or query encoding is usually wrong.

**Fix:** Verify these rules:

1. Use a WorkIQ path, not a full URL: `https://example.com/v1.0/me/messages` ❌, `/me/messages` ✅
2. Do not include a version prefix: `/v1.0/me/messages` ❌, `/me/messages` ✅
3. URL-encode OData query values: spaces as `%20`, quotes as `%27`, colons as `%3A`
4. Quote the path so the shell does not interpret `$`, `?`, or `&`

```powershell
workiq fetch -u '/me/messages?$top=10&$filter=isRead%20eq%20false'
```

## HTTP 404 path not found

**Symptom:** A command returns `HTTP 404`, `not found`, or says the path does not exist.

**Cause:** The path is misspelled, not available in the selected backend, or not supported for the current account.

**Fix:** Discover paths first, then inspect the schema.

```powershell
workiq search-paths -f 'messages'
workiq get-schema -u '/me/messages' -m get
```

## Shell quoting issues with `$`, `?`, and `&`

**Symptom:** The command behaves as if `$top`, `$filter`, or `$value` disappeared; PowerShell prompts for more input; or the shell treats `&` as a command separator.

**Cause:** The shell interpreted special URL characters before `workiq` received them.

**Fix:** Use single quotes around URL and JSON arguments.

```powershell
workiq fetch -u '/me/messages?$top=10&$select=subject,from'
workiq fetch-blob -u '/me/photo/$value'
workiq create -u '/me/events' -b '{"subject":"Standup","start":{"dateTime":"2026-05-26T15:00:00","timeZone":"Pacific Standard Time"},"end":{"dateTime":"2026-05-26T15:30:00","timeZone":"Pacific Standard Time"}}'
```

In bash, single quotes work the same way for these examples.

## `workiq ask` output is hard to parse

**Symptom:** `workiq ask` prints streaming markdown or conversational text instead of structured fields.

**Cause:** `--json` was not used.

**Fix:** Use `--json` for skill and automation scenarios. Add `-v` when you also need request IDs and conversation IDs.

```powershell
workiq ask --json -q 'Summarize my unread messages from today.'
workiq ask --json -v -q 'What meetings do I have tomorrow?'
```

## `workiq ask` is slow or appears to hang

**Symptom:** A single `workiq ask` command takes 10-30 seconds.

**Cause:** This is often expected. Natural-language queries may perform multiple searches internally.

**Fix:** If you only need a literal list, filter, or known entity, use `workiq fetch` or another structured command instead.

```powershell
workiq fetch -u '/me/messages?$top=10&$select=subject,from,receivedDateTime'
```

## `workiq ask` times out on broad questions

**Symptom:** `workiq ask` fails after a long wait or repeatedly hits request limits on complex questions.

**Cause:** The question is too broad, such as asking for every discussion about every project this month.

**Fix:** Break the request into smaller questions by person, project, or time window, then synthesize the answers locally.

```powershell
workiq ask --json -q 'Summarize Project Alpha emails from this week.'
workiq ask --json -q 'Summarize Project Alpha meetings from this week.'
```

## Multiple WorkIQ versions share configuration

**Symptom:** Config changes appear to be lost, ignored, or unexpectedly applied after switching between stable, preview, local, or globally installed WorkIQ binaries.

**Cause:** WorkIQ binaries can share the same user config directory.

**Fix:** Standardize on one installed CLI version when possible. If config looks corrupted, inspect or reset it, then sign in and consent again.

```powershell
workiq config list
workiq config reset
workiq auth login
```
