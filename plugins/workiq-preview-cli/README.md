# Work IQ — CLI variant

> Query Microsoft 365 data via the **WorkIQ CLI** (`workiq`) — emails, meetings, documents, Teams messages, and more. **No MCP server**: this plugin teaches the agent to invoke shell commands directly.

This plugin is a CLI-driven alternative to the `workiq-preview` plugin. Use it when:

- You don't want to run an MCP server (lower memory footprint, no long-running process)
- You prefer the agent to call WorkIQ via shell commands you can read in the transcript
- You want to use the same tool from scripts and from interactive prompts

## Prerequisites

- **Node.js 18+** and **npm** — see [skills/workiq-preview-cli/references/install-prerequisites.md](./skills/workiq-preview-cli/references/install-prerequisites.md)
- The `@microsoft/workiq@preview` package installed **globally** so the `workiq` binary is on `PATH`:

  ```powershell
  npm install -g @microsoft/workiq@preview
  workiq version
  workiq auth login
  ```

## Installation

### Via the Copilot CLI plugin marketplace

```powershell
copilot plugin marketplace add microsoft/work-iq
copilot plugin install workiq-preview-cli@work-iq
```

Then **restart your Copilot CLI session**.

## Updating

```powershell
npm update -g @microsoft/workiq@preview
workiq version
```

## Usage

Ask the agent any of the prompts below — the skill teaches it which `workiq` command to run:

```text
# Emails
"What did John say about the proposal?"
"List my unread emails from this week"

# Meetings
"What's on my calendar tomorrow?"

# Documents
"Find my recent PowerPoint presentations"

# Teams
"Summarize today's messages in the Engineering channel"

# Writes
"Create a calendar event Friday at 3pm with Sarah"
```

## Skills

| Skill | Description |
|-------|-------------|
| [**workiq-preview-cli**](./skills/workiq-preview-cli/SKILL.md) | Drive the WorkIQ CLI to query and modify M365 data |

## Platform Support

Supported on `win_x64`, `win_arm64`, `linux_x64`, `linux_arm64`, `osx_x64`, and `osx_arm64`.

## License

See the root [LICENSE](../../LICENSE) file.
