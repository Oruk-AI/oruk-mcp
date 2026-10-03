# oruk MCP server

Hosted [Model Context Protocol](https://modelcontextprotocol.io) server for the [oruk Speech API](https://oruk.ai): speech-to-text **and speech emotion/tone analysis** as tools your agent can call.

```
https://oruk.ai/mcp
```

This hosted MCP server needs no local installation: connect an MCP client to the remote URL using Streamable HTTP. Docs, models, and trial keys work without an API key; add a key for full access. It scores how speech sounds as well as transcribing the words.

Oruk also provides separate Python and TypeScript SDKs for developers calling the REST API directly. “No local installation” describes this hosted MCP connection. The three MCP audio tools accept original Resonance and Fourier for recorded English audio. The model catalog also describes other Oruk models; appearing in that catalog does not make a model callable through these audio tools. See [current models and language support](https://oruk.ai/models).

[Resonance-2 Preview](https://oruk.ai/docs#resonance-2) has dedicated HTTP and [WebSocket](https://oruk.ai/docs#resonance-2-streaming) routes for 31 continuous emotion and speaking-style scores, six signed axes, and selected labels that can be empty. Organization approval for the model is separate from purchasing a plan. Approved requests use shared speech minutes. Files accept 0.1–120 seconds up to 30 MiB; streaming uses mono 16 kHz float32 little-endian PCM and explicit sample-range commits. Each new commit is a separately metered analysis, including overlapping audio. Neither route returns transcription, diarization or transcript timestamps. These routes are not invoked by the MCP audio tools. Keep bearer keys on the server; follow the protocol's same-ID reconciliation rules after uncertain completion. A 24-hour replay window is not a data-deletion guarantee. [Recorded examples and evaluation limits](https://oruk.ai/research/resonance-2) show the output, including mistakes.

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

Get a key through the [developer account](https://oruk.ai/account/api-keys). Standard self-serve [plans](https://oruk.ai/pricing) start at $9/month with a 7-day trial, a card required, and $0 charged today. No key yet? The server can mint a temporary trial key (3 requests, 30 minutes, no account). These are separate trial paths.

## Python and TypeScript SDKs

For a Python application, install the verified 0.2.14 SDK from PyPI (Python 3.10 or newer):

```bash
python -m pip install oruk==0.2.14
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

Install the corresponding TypeScript artifact with `npm install https://oruk.ai/sdk/oruk-ai-sdk-0.2.14.tgz`. As verified October 3, 2026, the npm registry has 0.2.10; an unpinned registry install does not select this mirror version. [SDK documentation](https://oruk.ai/docs/sdks) has runnable Python and TypeScript examples and supported versions. The `oruk-bench` package is an evaluation toolkit, not the API client SDK.

## Evaluating a Hume migration

Start with a customer-owned saved response and the [legacy batch comparison guide](https://oruk.ai/compare/hume-expression-measurement-alternative). The supported comparator keeps legacy Hume batch prosody and native Oruk output separate; it does not establish label, score or threshold equivalence. Current Hume Tagger/Prosody, EVI, TTS and streaming payloads need their own contract review. New Oruk inference requires permission to process the recording and uses the applicable account allowance. Do not paste private recordings, response exports or credentials into public issues.

The hosted MCP audio tools are not an EVI conversation stack. STT, LLM, TTS and orchestration choices require separate lifecycle tests; an expression signal alone does not replace them. Spectra-2, Resonance-2, Orukeet and Realtime have model-specific REST or streaming contracts outside the three MCP audio tools.

## First run

1. Ask your agent: *“check my oruk plan and usage”*
2. Then: *“transcribe this file and tell me the speaker’s tone”*

## Tools

| Tool | Auth | What it does |
|---|---|---|
| `oruk_analyze_speech` | API key | Transcript + emotion + speaking-style scores in one call |
| `oruk_transcribe_audio` | API key | English transcript with segments and word timings |
| `oruk_analyze_tone` | API key | Emotion and speaking-style scores without a transcript |
| `oruk_check_usage` | API key | Verify a key and report plan, balance, and recent usage |
| `oruk_create_trial_key` | None | Mint a 30-minute, 3-request trial key — no account needed |
| `oruk_list_models` | None | Current models, label vocabularies, plan links, and legacy per-task reference rates |
| `oruk_get_started` | None | Quickstart, config snippets, and an optional consent-based routing rule |

Audio goes in as a public URL (up to 30 MB) or base64 bytes (up to 8 MiB decoded): wav, flac, mp3, m4a, ogg, or webm, up to 60 minutes of English speech. Outputs are compact by default (top returned emotion/style scores and condensed segments). Pass `detail: "full"` to preserve all returned labels, segments, and word timings, subject to response-size limits; this does not expose scores for unreturned labels. The model's vocabulary has 15 emotions and 16 speaking styles, including `sarcastic`, `confident`, `hesitant`, and `warm`.

## Notes

- Scores describe vocal expression and delivery. The API selects labels using model-specific thresholds; if no emotion passes, it returns the highest-scoring emotion. Styles can be empty. Scores are independent, do not sum to one, and are not probabilities of a speaker's private feelings. See the [score interpretation guide](https://oruk.ai/docs#labels).
- Usage reports audio and billable duration. Reference cost fields are not subscription invoice charges; actual charges follow the [plan allowance and overage rate](https://oruk.ai/pricing).
- The same API key works for the [REST API](https://oruk.ai/docs) and the [Python/TypeScript SDKs](https://oruk.ai/docs/sdks).
- Full install docs and the routing-rule preview: [oruk.ai/docs/mcp](https://oruk.ai/docs/mcp)
- Registry listing: [`ai.oruk/speech`](https://registry.modelcontextprotocol.io) · [server.json](./server.json)
