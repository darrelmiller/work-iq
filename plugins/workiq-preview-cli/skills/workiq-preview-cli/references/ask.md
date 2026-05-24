# workiq ask

Query Microsoft 365 Copilot for workplace intelligence using natural language. This is the primary command for M365 data questions: emails, meetings, documents, Teams messages, calendar, and people.

For global flags such as `--account`, `--endpoint`, headers, and log level, see `cli-commands.md`.

## Flags

| Flag | Required | Description |
|------|----------|-------------|
| `-q`, `--question <text>` | Yes | Natural language question. Be specific about people, topics, and timeframes. |
| `--json` | No | Output structured JSON. **Recommended for skill use** because it is easier to parse than markdown. |
| `-f`, `--file-urls <url>` | No | OneDrive or SharePoint file URL to use as context. Repeat for multiple files. |
| `-c`, `--conversation-id <id>` | No | Continue an existing conversation. Use an ID from a prior `--json` or verbose response. |
| `--agent-id <id>` | No | Target a specific agent by ID. |
| `--agent-name <name>` | No | Target a specific agent by name. |
| `-v`, `--verbose` | No | Include request ID and conversation ID in output. |
| `--streaming` | No | Experimental streaming responses. Avoid when machine-parsing output. |

## When to Use

Use `workiq ask` when:

- You need information that exists somewhere in M365: emails, meetings, documents, Teams, calendar, or people
- The user asks what someone said, shared, decided, or communicated
- You need organizational context before implementing something
- The question is open-ended or exploratory

Prefer `workiq ask --json` for open-ended questions. Switch to commands such as `workiq fetch` when you need precise structured data or need to write/modify data.

## Examples

### People and expertise

```powershell
workiq ask --json -q 'Who is the expert on authentication in our team?'
workiq ask --json -q 'What has Sarah been focused on lately?'
workiq ask --json -q 'What are the latest top-of-mind items from Rob I should know about?'
```

### Meetings and decisions

```powershell
workiq ask --json -q 'What decisions were made in my meeting last week about the new feature?'
workiq ask --json -q 'What action items came out of sprint planning?'
workiq ask --json -q 'Summarize the architecture discussion from yesterday standup.'
```

### Emails and messages

```powershell
workiq ask --json -q 'Any recent emails from Rob about the deadline?'
workiq ask --json -q 'What did the team discuss in Teams about the release?'
workiq ask --json -q 'Summarize my unread messages from today.'
```

### Documents and specs

```powershell
workiq ask --json -q 'Find the design doc for the authentication system.'
workiq ask --json -q 'What is the latest spec for Project X?'
workiq ask --json -q 'Where is the API documentation for the payments service?'
```

### Calendar and schedule

```powershell
workiq ask --json -q 'What meetings do I have today?'
workiq ask --json -q 'What is on my calendar tomorrow?'
```

### Use file context

```powershell
workiq ask --json -q 'Summarize open questions in this spec.' -f 'https://contoso.sharepoint.com/sites/team/Shared%20Documents/spec.docx'
```

### Continue a conversation

```powershell
workiq ask --json -q 'Now turn those into implementation tasks.' -c 'conversation-id-from-previous-response'
```

### Target an agent

```powershell
workiq ask --json -q 'Evaluate this rollout plan.' --agent-name 'Project Analyst'
workiq ask --json -q 'Summarize risks.' --agent-id 'agent-id-here'
```

## Output Tips

- Always use `--json` when another agent needs to parse the response.
- Use `-v` when you need the request ID or conversation ID for diagnostics.
- Without `--json`, output may be streaming markdown and harder to consume reliably.
