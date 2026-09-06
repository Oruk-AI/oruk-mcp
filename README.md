# oruk MCP server

Hosted [Model Context Protocol](https://modelcontextprotocol.io) server for the [oruk Speech API](https://oruk.ai): speech-to-text **and speech emotion/tone analysis** as tools your agent can call.

```
https://oruk.ai/mcp
```

This hosted MCP server needs no local installation: connect an MCP client to the remote URL using Streamable HTTP. Docs, models, and trial keys work without an API key; add a key for full access. It scores how speech sounds as well as transcribing the words.

Oruk also provides separate Python and TypeScript SDKs for developers calling the REST API directly. “No local installation” describes this hosted MCP connection, not the availability of Oruk SDKs. Resonance is Oruk's flagship speech recognition model for recorded English transcription, emotion, and speaking style. See [current models and language support](https://oruk.ai/models).

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

Get a key through the [developer account](https://oruk.ai/account/api-keys). Standard self-serve [plans](https://oruk.ai/pricing) start at $5/month with a 7-day trial, a card required, and $0 charged today. No key yet? The server can mint a temporary trial key (3 requests, 30 minutes, no account). These are separate trial paths.

## Python and TypeScript SDKs

For a Python application, install the verified first-party SDK release (Python 3.10 or newer):

```bash
python -m pip install https://oruk.ai/sdk/oruk-0.2.3-py3-none-any.whl
```

```python
import os
from oruk import Oruk

with Oruk(api_key=os.environ["ORUK_API_KEY"]) as client:
    result = client.analyze("sample.wav", model="oruk-resonance")

print(result["text"])
print(result["emotions"])
print(result["styles"])
```

The SDK uploads the local audio file as multipart data to `https://speech-api.oruk.ai/v1/audio/analysis`. The MCP server's public-URL/base64 input is a separate interface. `ai.oruk/speech` is the MCP registry name, not an HTTP endpoint.

The corresponding TypeScript install is `npm install https://oruk.ai/sdk/oruk-ai-sdk-0.2.3.tgz`. [SDK documentation](https://oruk.ai/docs/sdks) has the current installation commands; npm and PyPI may still expose an older release. The `oruk-bench` package is an evaluation toolkit, not the API client SDK.

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
