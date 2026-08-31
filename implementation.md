Yes — now I can see exactly where you are. **You are already in the correct Exotel Flow Builder.** Your second screenshot shows the **Voicebot** applet, which is the right applet for connecting your own AI server to a live phone call. Exotel's current documentation confirms that the Voicebot applet supports bidirectional WebSocket audio: caller audio goes to your server and your server sends audio back to the caller. ([Exotel Docs][1])

### Important: you do NOT need `EXOTEL_STREAM_TOKEN`

For the setup you're showing, **don't look for a Stream Token in Exotel**.

Your Voicebot box is asking for:

> **"Enter a http(s) URL if you want to return a different ws(s) endpoint..."**

That means you need to give Exotel a URL belonging to **your server**.

For example:

```text
https://your-domain.com/exotel/voicebot
```

or, if you're directly providing the WebSocket:

```text
wss://your-domain.com/media
```

Exotel then establishes the live audio connection to your server. ([Exotel Docs][1])

---

# What you should do now

Your architecture should be:

```text
                  📞
             Your phone
                 │
                 │ calls
                 ▼
        ┌──────────────────┐
        │ Exotel Number    │
        │ 09513886363      │
        └────────┬─────────┘
                 │
                 ▼
        ┌──────────────────┐
        │ Exotel Flow      │
        │                  │
        │ Call Start       │
        │      ↓           │
        │   Voicebot       │
        └────────┬─────────┘
                 │
                 │ WebSocket
                 ▼
        ┌──────────────────────┐
        │ YOUR INTERNET SERVER │
        │                      │
        │ /media               │
        │                      │
        │ Audio                │
        │   ↓                  │
        │ STT                  │
        │   ↓                  │
        │ YOUR LLM             │
        │   ↓                  │
        │ TTS                  │
        │   ↓                  │
        │ Audio                │
        └──────────┬───────────┘
                   │
                   ▼
                 Exotel
                   │
                   ▼
                 📞
```

This is exactly what Exotel's current AgentStream/Voicebot system is designed for. ([Exotel Docs][2])

---

# Step 1 — Don't put your LLM API here

In this box:

```text
Which bot you want to connect the enduser?

[                                      ]
```

**Do NOT put:**

```text
Gemini API key
LLM API key
EXOTEL_STREAM_TOKEN
your model name
```

Instead, put your **public HTTPS endpoint**.

For example:

```text
https://api.myserver.com/exotel/voicebot
```

Your server then responds with the WebSocket endpoint.

Exotel's documentation specifically supports this dynamic method: an HTTPS endpoint can return a `wss://` endpoint for the call. ([Exotel Docs][1])

---

# Step 2 — Your server needs TWO endpoints

I recommend building these:

### Endpoint 1

```text
POST/GET
https://api.yourserver.com/exotel/voicebot
```

Its job is essentially:

```text
Exotel
   ↓
/exotel/voicebot
   ↓
"Connect to this WebSocket"
   ↓
wss://api.yourserver.com/exotel/media
```

### Endpoint 2

```text
wss://api.yourserver.com/exotel/media
```

This is the **actual live audio connection**.

Your AI lives behind this.

---

# Step 3 — The important part: `/media`

The WebSocket will receive the caller's audio.

Conceptually:

```python
@app.websocket("/exotel/media")
async def exotel_media(websocket):

    await websocket.accept()

    while True:

        audio = await websocket.receive_bytes()

        text = await speech_to_text(audio)

        response = my_local_llm(text)

        audio_response = text_to_speech(response)

        await websocket.send_bytes(audio_response)
```

That's simplified, but that's the architecture.

Exotel's current AgentStream documentation says bidirectional Voicebot streaming uses WebSockets and supports the bot sending audio back over the same connection. ([Exotel Docs][1])

---

# Step 4 — Authentication

This is where your previous:

```env
EXOTEL_STREAM_TOKEN=
```

question becomes important.

**You don't need to invent an `EXOTEL_STREAM_TOKEN` unless your own server code specifically wants one.**

Exotel currently documents two authentication approaches for the WSS endpoint:

### Option A — IP whitelisting

You allow Exotel's outbound IP ranges to connect.

### Option B — Basic Authentication

Your WebSocket URL can be configured with your Exotel API credentials, and Exotel sends them as an Authorization header. ([Exotel Docs][1])

So your `.env` should primarily contain:

```env
EXOTEL_ACCOUNT_SID=...
EXOTEL_API_KEY=...
EXOTEL_API_TOKEN=...
```

rather than:

```env
EXOTEL_STREAM_TOKEN=...
```

**Keep those credentials private.**

---

# Step 5 — Your existing model fits perfectly

You told me you already have:

> your own locally trained/fine-tuned language model on an internet-hosted server.

That's actually ideal.

You don't have to change your LLM.

Your server can be:

```text
                 PHONE
                   │
                   ▼
                EXOTEL
                   │
                   │ audio
                   ▼
             ┌───────────┐
             │   STT     │
             └─────┬─────┘
                   │
                   ▼
             ┌───────────┐
             │ YOUR 2B   │
             │    LLM    │
             └─────┬─────┘
                   │
                   ▼
             ┌───────────┐
             │   TTS     │
             └─────┬─────┘
                   │
                   │ audio
                   ▼
                EXOTEL
                   │
                   ▼
                 PHONE
```

The only new component you're adding is the **telephony + real-time audio layer**.

---

## One thing I notice in your screenshot

Your current Exotel flow is:

```text
Call Start
     ↓
Voicebot
```

That's fine for the first prototype.

You can later make:

```text
Call Start
     ↓
Greeting
     ↓
Voicebot
     ↓
Passthru / Connect
     ↓
Human operator
```

if you want your AI to transfer difficult cases to a human. Exotel's current Flow API documentation explicitly supports Voicebot followed by Connect/Transfer patterns. ([Exotel Docs][3])

---

## ⚠️ One prerequisite

Your Exotel dashboard is showing a **free trial account**.

The current AgentStream documentation says that voice streaming access needs to be enabled on the account, and its quickstart notes that Indian accounts need KYC completion before AgentStream access is enabled. ([Exotel Docs][4])

So if you configure everything correctly but Exotel refuses to establish the WebSocket, **this may be an account-feature/KYC issue rather than a coding problem.**

---

### What I recommend we do next

Since you're already on the exact screen, **don't change anything else in Exotel yet.**

Give me these 3 things:

1. **Your backend technology:** FastAPI / Flask / Node.js / something else
2. **What your LLM API looks like:** e.g. `POST /generate` → text response
3. **What STT and TTS you're currently using**, if any

Then I can give you the **complete working Exotel backend**, including:

```text
/exotel/voicebot
/exotel/media
      ↓
Exotel audio protocol
      ↓
STT
      ↓
your 2B LLM
      ↓
TTS
      ↓
Exotel
```

and tell you **exactly what URL to paste into the box shown in your screenshot**.

[1]: https://docs.exotel.com/exotel-agentstream/voicebot-applet?utm_source=chatgpt.com "Exotel Docs"
[2]: https://docs.exotel.com/exotel-agentstream?utm_source=chatgpt.com "Exotel Docs"
[3]: https://docs.exotel.com/exotel-agentstream/connect-voice-ai-with-flow-api?utm_source=chatgpt.com "Exotel Docs"
[4]: https://docs.exotel.com/exotel-agentstream/overview-and-quickstart?utm_source=chatgpt.com "Exotel Docs"
