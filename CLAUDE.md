# Cai Extensions

Community extensions repo for [Cai](https://getcai.app), a native macOS clipboard manager.

## Key Files

- `schema.yaml` — YAML format reference for all extension types
- `_local/AGENT-GUIDE.md` — detailed guide for creating extensions
- `_local/recommendations.md` — extension ideas organized by priority tier
- `extensions/` — all extensions, one folder each (kebab-case)

## Extension Format (Schema v2)

All extensions are YAML files starting with `# cai-extension` on the first line. Four types:

- `type: prompt` — LLM-powered (clipboard text sent with instruction)
- `type: url` — opens URL with `%s` replaced by clipboard text
- `type: webhook` — HTTP POST/PUT/PATCH to an endpoint
- `type: deeplink` — opens a URL scheme (e.g. `bear://`)

Shell and AppleScript types exist but are blocked from clipboard install for security.

## Creating an Extension

1. Create a folder under `extensions/` with a kebab-case name
2. Add `extension.yaml` inside it — must start with `# cai-extension`
3. Required fields: name, description, author, version, tags, icon, type
4. Author for seed extensions: `clipboard-ai`
5. Icons use SF Symbol names (e.g. `envelope.fill`, `magnifyingglass`)
6. For prompt types: the system prompt already says "Output ONLY the processed text" — don't repeat that. Add "Do not use any markdown formatting" since output goes to clipboard.

## Prompt Tips

- Be specific about format and what to preserve
- Don't add preambles like "The user has copied:" — Cai handles that
- Don't include `{{text}}` or `{{clipboard}}` placeholders — text is passed automatically
- Test with various inputs: single word, sentence, paragraph, long text

## Repo Setup

- Branch protection via GitHub rulesets (admin bypass enabled)
- `ADMIN_TOKEN` PAT used in build-index workflow
- CI auto-generates `index.json` on push to master when `extensions/**` changes
- PRs from outside contributors require 1 approving review

## Security Rules

- No `applescript` or `shell` types in community contributions
- Webhook extensions should use `{{setup_field}}` for URLs, never hardcoded
- No API keys in YAML — use `setup` fields with `secret: true`
