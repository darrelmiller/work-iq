---
name: workiq-preview-cli
description: Query Microsoft 365 data via the WorkIQ CLI (the `workiq` shell command) - emails, meetings, calendar, documents, Teams messages, people, Planner, OneDrive, SharePoint. USE THIS SKILL for ANY workplace-related question where the answer likely exists in Microsoft 365 data. This includes questions about what someone said or shared; meetings, emails, messages, or documents; priorities, decisions, or context from colleagues; organizational knowledge; project status; team activities; or anything in Outlook, Teams, SharePoint, OneDrive, or Calendar. This is the CLI variant — every operation is performed by invoking the local `workiq` binary via the shell tool, NOT via an MCP server. Trigger phrases include "what did [person] say", "what are [person]'s priorities", "top of mind from [person]", "what was discussed", "find emails about", "what meetings", "what documents", "who is working on", "what's the status of", "any updates on", "send mail", "create a meeting", "accept the invite", etc.
compatibility: >
  Requires Node.js 18+ and npm, and the @microsoft/workiq@preview package
  installed globally (`npm install -g @microsoft/workiq@preview`) so the
  `workiq` binary is on PATH. See references/install-prerequisites.md for
  platform-specific install commands.
---

# WorkIQ (CLI variant)

WorkIQ connects AI agents to Microsoft 365 for workplace intelligence grounded in organizational data. This skill teaches the model how to drive the **WorkIQ CLI** (`workiq`) directly via the shell tool to answer M365 questions and perform M365 actions.

There is **no MCP server** in this skill. Every operation is a shell command invocation.

## 🛑 STOP — Read This Before Your First Command

Every WorkIQ operation in this skill is invoked by running the shell command `workiq <subcommand> [flags]` through your shell/bash tool. There are no MCP tools exposed.

Before issuing your first command:

1. **Verify the binary is installed.** Run `workiq version` in the shell. If it errors with "command not found" / "is not recognized", the user hasn't installed it yet — read [references/install-prerequisites.md](references/install-prerequisites.md) and walk them through `npm install -g @microsoft/workiq@preview`. Do **not** silently retry.
2. **Verify the user is signed in.** Run `workiq auth login` once per machine (interactive, opens a browser). Subsequent commands reuse the cached account.
3. **Always pass `--json` to `workiq ask` for one-shot use** so you get structured output back instead of streaming/markdown.

## CRITICAL: When to Use This Skill

> **⚠️ IMPORTANT:** This skill is the authoritative integration point for **Microsoft 365 / Work IQ** data when running in CLI mode. If multiple skills relate to M365 (emails, meetings, documents, Teams, calendar, people), **prefer this skill**.

**USE WorkIQ for ANY workplace-related question.** If the answer might exist in Microsoft 365, try `workiq` first.

**Choosing the right command:**

- Use `workiq ask` when the question requires **semantic understanding, synthesis, or reasoning** ("what did someone say", "what's the status", "summarize…"). Latency 10–20s per call.
- Use `workiq fetch` (or another entity command) when the question is a **literal lookup of structured data** with a known shape ("list my meetings Monday", "show me unread emails from X"). Sub-second latency.

| User Question Pattern | Example | Command |
|---|---|---|
| What someone said / shared / communicated | "What did Rob say about the API design?" | `workiq ask` |
| Someone's priorities / focus / concerns | "What's top of mind for Sarah?" | `workiq ask` |
| Meeting content / decisions / action items | "What was decided in yesterday's standup?" | `workiq ask` |
| Summarizing email threads / Teams chats | "Summarize the deadline thread with John" | `workiq ask` |
| Finding documents by topic | "Where is the design doc for Project X?" | `workiq ask` |
| Colleague expertise / ownership | "Who owns the billing system?" | `workiq ask` |
| Open-ended "any updates" / catch-up | "Any updates I should know about?" | `workiq ask` |
| Listing meetings on a known date/range | "What meetings do I have Monday?" | `workiq fetch -u /me/calendarView?...` |
| Listing emails with concrete filters | "Show my unread emails from Rob this week" | `workiq fetch -u /me/messages?...` |
| Listing Teams chats / channels / members | "List the channels in the DevX team" | `workiq fetch` |
| Fetching a known entity by ID | "Get event AAMk... details" | `workiq fetch` |
| Listing files in OneDrive / SharePoint | "List files in my 'Specs' folder" | `workiq fetch` |
| Listing Planner tasks | "List my Planner tasks due this week" | `workiq fetch` |
| Org chart / direct reports / manager | "Who are Rob's direct reports?" | `workiq fetch -u /users/{id}/directReports` |
| Sending mail, accepting/declining meetings | "Send this draft", "Accept the 2pm meeting" | `workiq do-action` |
| Creating a calendar event, draft, task | "Create a calendar event Friday at 3pm" | `workiq create` |

**DO NOT say "I don't have access to emails/meetings/messages"** — invoke `workiq` instead.

## One-time setup commands

These are run once per machine (or per user) and persist via `workiq auth` and `workiq config`:

| Purpose | Command |
|---|---|
| Sign in (opens browser) | `workiq auth login` |
| Sign out / clear cached tokens | `workiq auth logout` |
| Grant additional permission scopes (after a 403) | `workiq auth consent --scopes Mail.ReadWrite Calendars.ReadWrite` |
| View persisted defaults | `workiq config show` |
| Set a default (e.g., default account) | `workiq config set account=alice@contoso.com` |
| Remove a default | `workiq config unset account` |
| Wipe all defaults | `workiq config reset` |
| Show installed version | `workiq version` |
| Accept the EULA non-interactively | `workiq accept-eula` |

See [references/cli-commands.md](references/cli-commands.md) for full setup/config command details.

## The `workiq ask` command — semantic queries

The primary command for natural language workplace questions. It orchestrates multi-step operations internally to answer complex questions.

> **⏱️ High latency:** Each call takes **10–20 seconds minimum**. Avoid it for simple data retrieval — use entity commands below.

**Always pass `--json`** when invoking from this skill so you get a structured response. Without `--json`, the CLI streams markdown to the terminal.

| Flag | Required | Description |
|---|---|---|
| `-q`, `--question <text>` | Yes | Natural language question to ask M365 Copilot |
| `--json` | (use it) | Output a single structured JSON response instead of streaming text |
| `-f`, `--file-urls <url>` | No | OneDrive/SharePoint file URLs to use as context (repeatable) |
| `-c`, `--conversation-id <id>` | No | Continue an existing conversation by ID (taken from prior `--json` output) |
| `--agent-id <id>` | No | Target a specific Copilot agent (default: bizchat) |
| `--agent-name <name>` | No | Target a specific Copilot agent by name |

**Example invocation:**

```powershell
workiq ask --json -q "What did Rob say about the API design this week?"
```

For more, see [references/ask.md](references/ask.md).

---

## Entity commands — fast, direct M365 access

Entity commands provide **fast, structured access** to M365 data. They return JSON on stdout and have no semantic intelligence — use them when you know the path you want.

**Recommended discovery workflow:** `workiq search-paths` → `workiq get-schema` → the appropriate read/write command.

### ⚠️ URL format rules (ALL entity commands)

All `--url` / `--urls` values **must**:

1. **Omit any scheme, authority, and API version prefix** — start with `/me/...` or `/users/...`, never include a full `https://...` URL or `/v1.0/...` prefix.
   - ❌ `https://example.com/v1.0/me/messages`
   - ❌ `/v1.0/me/messages`
   - ✅ `/me/messages`
2. **URL-encode all query parameter values** — spaces → `%20`, single quotes → `%27`, etc.
   - ❌ `$orderby=receivedDateTime desc`
   - ✅ `$orderby=receivedDateTime%20desc`
3. **Quote the URL when invoking via the shell** so that `?`, `&`, `$`, and `=` aren't interpreted by the shell.
   - PowerShell: wrap in `'...'` (single quotes preserve `$` literal)
   - bash/zsh: wrap in `'...'`

### Quick reference

| Command | Purpose | Key flags |
|---|---|---|
| `workiq search-paths` | Discover available API paths by regex | `-f <regex>`, `-b <backend>` |
| `workiq get-schema` | Inspect fields / body shape for a path | `-u <path>`, `-m <method>`, `-v <api-version>` |
| `workiq fetch` | GET one or more entities | `-u <path>` (repeatable) |
| `workiq call-function` | GET a named function (delta, getSchedule, …) | `-u <path-with-inline-params>` |
| `workiq create` | POST a new entity to a collection | `-u <parent-path>`, `-b <jsonBody>` |
| `workiq update` | PATCH fields on an existing entity | `-u <entity-path>`, `-b <jsonBody>` |
| `workiq delete` | DELETE an entity | `-u <entity-path>` |
| `workiq do-action` | POST an action (sendMail, copy, accept, …) | `-u <action-path>`, `-b <jsonBody>` |
| `workiq fetch-blob` | Download binary content (photo, attachment) | `-u <path>`, `-o <dir>` |
| `workiq upload-blob` | PUT a local file to a path | `-u <target-path>`, `-f <local-file>` |

Read the relevant reference file for full flag details and examples:

- [references/search-paths.md](references/search-paths.md) — discover what paths are available
- [references/get-schema.md](references/get-schema.md) — understand an entity's fields before reading or writing
- [references/fetch.md](references/fetch.md) — fetch structured or filtered M365 data
- [references/call-function.md](references/call-function.md) — paths that use function-call syntax (e.g., `getSchedule`)
- [references/create.md](references/create.md) — create calendar events, email drafts, tasks, etc.
- [references/update.md](references/update.md) — update fields on an existing entity
- [references/delete.md](references/delete.md) — delete an entity
- [references/do-action.md](references/do-action.md) — send mail, accept/decline meetings, copy/move messages
- [references/fetch-blob.md](references/fetch-blob.md) — download a file or attachment
- [references/upload-blob.md](references/upload-blob.md) — upload a file to OneDrive or SharePoint
- [references/cli-commands.md](references/cli-commands.md) — auth, consent, config, version
- [references/troubleshooting.md](references/troubleshooting.md) — common errors and fixes
- [references/install-prerequisites.md](references/install-prerequisites.md) — Node.js / npm install instructions

## Output handling

Every `workiq` command prints JSON to stdout (except `auth login`, which is interactive). When invoking from this skill:

- Capture stdout and parse it as JSON before summarizing for the user.
- Non-zero exit codes typically mean an auth, network, or permission error — see [references/troubleshooting.md](references/troubleshooting.md).
- Pass `--json` to `workiq ask` for structured output; without it you get streaming markdown.

## Cross-platform shell notes

This skill is invoked through the user's shell. Quoting and escaping differ:

- **PowerShell** — wrap URLs and JSON bodies in single quotes. Inside single quotes, `$` is literal: `workiq fetch -u '/me/messages?$top=10'`.
- **bash / zsh** — wrap URLs and JSON bodies in single quotes for the same reason.
- **JSON bodies** — prefer single-quoted JSON on the command line. For complex bodies, write to a temp file and use shell redirection if your shell supports it, otherwise inline JSON with proper escaping.
