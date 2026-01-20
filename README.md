# AI Bot (FastAPI + Azure OpenAI)

Node.js v20 compatible.

Behavior:
- Text input -> text reply
- Audio input -> audio reply
- ACS WhatsApp inbound (Event Grid) -> auto reply via ACS Advanced Messaging

This version includes working Azure OpenAI REST calls for:
- GPT text (Chat Completions)
- STT (Audio Transcriptions)
- TTS (Audio Speech)

Flow:
Text -> GPT -> Text
Audio -> STT -> GPT -> TTS -> Audio

## Setup
1) Install Python deps
```bash
pip install -r requirements.txt
```

2) Create env file
```bash
cp .env.example .env
```

3) Fill:
- Azure OpenAI endpoint/key
- Azure deployment names:
  - `AZURE_GPT_DEPLOYMENT`
  - `AZURE_STT_DEPLOYMENT`
  - `AZURE_TTS_DEPLOYMENT`
- Azure Communication Services (ACS) WhatsApp:
  - `ACS_CONNECTION_STRING`
  - `ACS_CHANNEL_REGISTRATION_ID`
  - Optional: `EVENTGRID_SECRET` (requires header `x-eventgrid-secret`)

4) Run
```bash
uvicorn main:app --host 0.0.0.0 --port 8000
```

## API
- `GET /health`
- `POST /text` with JSON: `{ "text": "hello" }`
- `POST /audio` with multipart form file field named `file`
- `POST /acs/events` Event Grid webhook (list of events, or a single event)
  - Subscription validation response uses `validationResponse`
  - AdvancedMessageReceived triggers auto-reply: "Got your message ✅ (via ACS)"

## Notes
- The WhatsApp Cloud API (Meta) Node.js logic lives in `app/`.
- If you re-enable WhatsApp, ensure your TTS format matches the media content type.
