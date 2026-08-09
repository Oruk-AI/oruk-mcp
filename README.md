# oruk MCP server

Hosted [Model Context Protocol](https://modelcontextprotocol.io) server for the [oruk Speech API](https://oruk.ai): speech-to-text **and speech emotion/tone analysis** as tools your agent can call.

```
https://oruk.ai/mcp
```

No local install, no Python, no file-path sandbox — one remote URL (Streamable HTTP). Works keyless for docs, models, and trial keys; add an API key for full access. It is the only MCP that scores *how* something was said, not just the words.

mcp-name: ai.oruk/speech

## Install

**Cursor** — one click: [Add to Cursor](https://cursor.com/en/install-mcp?name=oruk&config=eyJ1cmwiOiJodHRwczovL29ydWsuYWkvbWNwIn0=), or add to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "oruk": {
      "url": "https://oruk.ai/mcp",
      "headers": { "Authorization": "Bearer YOUR_ORUK_API_KEY" }
    }
  }
}
```

**Claude Code**

```bash
claude mcp add --transport http oruk https://oruk.ai/mcp \
  --header "Authorization: Bearer YOUR_ORUK_API_KEY"
```

**Codex CLI**

```bash
codex mcp add oruk --url https://oruk.ai/mcp --bearer-token-env-var ORUK_API_KEY
```

**VS Code**

```bash
code --add-mcp '{"name":"oruk","type":"http","url":"https://oruk.ai/mcp","headers":{"Authorization":"Bearer YOUR_ORUK_API_KEY"}}'
```

**Windsurf** — note `serverUrl`, not `url`:

```json
{
  "mcpServers": {
    "oruk": {
      "serverUrl": "https://oruk.ai/mcp",
      "headers": { "Authorization": "Bearer YOUR_ORUK_API_KEY" }
    }
  }
}
```

Get a key at [oruk.ai/account](https://oruk.ai/account) — new accounts receive $50 in trial credit. No key yet? The server can mint itself a temporary trial key (3 requests, 30 minutes, no account).

## First run

1. Ask your agent: *“check my oruk credits”*
2. Then: *“transcribe this file and tell me the speaker’s tone”*

## Tools

| Tool | Auth | What it does |
|---|---|---|
| `oruk_analyze_speech` | API key | Transcript + emotion + speaking-style scores in one call |
| `oruk_transcribe_audio` | API key | English transcript with segments and word timings |
| `oruk_analyze_tone` | API key | Emotion and speaking-style scores without a transcript |
| `oruk_check_credits` | API key | Verify a key and report plan, balance, and recent usage |
| `oruk_create_trial_key` | None | Mint a 30-minute, 3-request trial key — no account needed |
| `oruk_list_models` | None | Models, per-task pricing, and the emotion/style label sets |
| `oruk_get_started` | None | Quickstart, config snippets, and an optional consent-based routing rule |

Audio goes in as a public URL or base64 bytes — wav, flac, mp3, m4a, ogg, or webm, up to 30 MB and 60 minutes of English speech. Outputs are compact by default (top emotion/style scores, condensed segments); pass `detail: "full"` for word-level timings. Emotion has 15 calibrated labels, speaking style has 16 (including `sarcastic`, `confident`, `hesitant`, `warm`).

## Notes

- Outputs are calibrated acoustic annotations of how speech sounds — not claims about a speaker’s inner state, and not a basis for consequential decisions on their own.
- The same API key works for the [REST API](https://oruk.ai/docs) and the [Python/TypeScript SDKs](https://oruk.ai/docs/sdks).
- Full install docs and the routing-rule preview: [oruk.ai/docs/mcp](https://oruk.ai/docs/mcp)
- Registry listing: [`ai.oruk/speech`](https://registry.modelcontextprotocol.io) · [server.json](./server.json)
