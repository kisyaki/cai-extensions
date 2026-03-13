# Cai Extensions

Community-contributed shortcuts and output destinations for [Cai](https://getcai.app), the macOS clipboard manager.

## Install an Extension

1. Browse the [`extensions/`](extensions/) directory and open the `extension.yaml` you want
2. Copy the entire YAML content (including the `# cai-extension` header)
3. Press **Option+C** — Cai detects it as an extension and shows **Install Extension**
4. Press Enter — done!

## Browse Extensions

Extensions are organized by category in the [`extensions/`](extensions/) directory.

## Create an Extension

Each extension is a single YAML file that starts with `# cai-extension` on the first line.

### Extension Types

| Type | What it does | Needs LLM? |
|------|-------------|------------|
| `prompt` | Sends clipboard text to the LLM with a custom instruction | Yes |
| `url` | Opens a URL with `%s` replaced by clipboard text | No |
| `webhook` | Sends text to an HTTP endpoint (Slack, Discord, etc.) | No |
| `deeplink` | Opens a URL scheme / deep link (Bear, Things, etc.) | No |
| `shell` | Runs a shell command with the text | No (local only) |
| `applescript` | Runs an AppleScript with the text | No (local only) |

### Prompt Shortcut

Sends clipboard text to the LLM with your instruction:

```yaml
# cai-extension
name: Professional Email
description: Rewrite text as a polished professional email
author: your-github-username
version: "1.0"
tags: [writing, email]
icon: envelope.fill
type: prompt
prompt: |
  Rewrite the following text as a professional email.
  Keep the core message but make it polished and clear.
  Include a greeting and sign-off.
```

### URL Shortcut

Opens a URL with `%s` replaced by the clipboard text:

```yaml
# cai-extension
name: Search StackOverflow
description: Search StackOverflow for the selected text
author: your-github-username
version: "1.0"
tags: [developer, search]
icon: magnifyingglass
type: url
url: "https://stackoverflow.com/search?q=%s"
```

### Webhook Destination

Sends text to an HTTP endpoint:

```yaml
# cai-extension
name: Send to Slack
description: Post text to a Slack channel via webhook
author: your-github-username
version: "1.0"
tags: [productivity, slack]
icon: bubble.left.fill
type: webhook
show_in_action_list: true
webhook:
  url: "{{slack_webhook_url}}"
  method: POST
  headers:
    Content-Type: application/json
  body: '{"text": "{{result}}"}'
setup:
  - key: slack_webhook_url
    label: Slack Webhook URL
    secret: false
```

### Deeplink Destination

Opens a URL scheme / deep link with the result text:

```yaml
# cai-extension
name: Save to Bear
description: Create a new note in Bear
author: your-github-username
version: "1.0"
tags: [productivity, notes]
icon: doc.text
type: deeplink
show_in_action_list: true
deeplink: "bear://x-callback-url/create?text={{result}}"
```

See [`schema.yaml`](schema.yaml) for the full format reference.

> **Security note:** `applescript` and `shell` destinations are supported in Cai but are **not accepted** via clipboard install or in this community repo — they allow arbitrary code execution. You can create them locally in Cai via Settings > Output Destinations.

## Submit an Extension

1. Fork this repo
2. Create a folder under `extensions/` with a kebab-case name (e.g. `professional-email`)
3. Add an `extension.yaml` file inside it (must start with `# cai-extension`)
4. Open a pull request

All PRs require review and approval before merging.

### Guidelines

- **One extension per folder** -- keep it focused
- **Start with `# cai-extension`** on the first line -- required for Cai to detect the extension
- **Test your extension** in Cai before submitting (copy the YAML, press Option+C)
- **Use SF Symbol names** for icons ([browse symbols](https://developer.apple.com/sf-symbols/))
- **Write a clear description** -- this is what users see when browsing
- **No API keys in YAML** -- use `setup` fields for secrets, which Cai stores in the user's Keychain
- **No `applescript` or `shell` destinations** -- these are not accepted for security reasons

## Required Fields

| Field | Description |
|-------|-------------|
| `name` | Display name shown in Cai |
| `description` | One-line summary for the extension browser |
| `author` | Your GitHub username |
| `version` | Version string (e.g. `"1.0"`) |
| `tags` | Array of lowercase tags for search/filtering |
| `icon` | SF Symbol name |
| `type` | `prompt`, `url`, `webhook`, or `deeplink` |

## Template Placeholders

| Placeholder | Used in | Description |
|-------------|---------|-------------|
| `%s` | URL shortcuts | Replaced with clipboard text (URL-encoded) |
| `{{result}}` | Destinations | Replaced with the text to send (auto-escaped per destination type) |
| `{{field_key}}` | Destinations | Replaced with user-configured setup field value |
