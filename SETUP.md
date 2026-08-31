# Sahakar AI — Run and call setup

## 1. Install and run locally

This project uses Node.js for both the React web app and the backend. Node 20 or later is recommended.

```powershell
npm install
Copy-Item backend\.env.example backend\.env
```

Edit `backend/.env` and add a Gemini API key:

```env
GEMINI_API_KEY=your_google_ai_studio_key
GEMINI_MODEL=gemini-3.7-flash
```

In one terminal:

```powershell
npm run dev:backend
```

In another terminal:

```powershell
npm run dev:frontend
```

Open [http://localhost:5173](http://localhost:5173). The frontend talks to Sahakar AI on port `8787`; the Gemini key never reaches the browser.

## 2. Web demo

1. Type a Marathi, Hindi, or English question and send it.
2. Click the microphone, speak, stop, then press Send.
3. Click **Listen** after the answer is shown.
4. Upload a PDF, TXT, JPG, PNG, or WEBP file (up to 10 MB), then ask a question about it.

Browser microphone and read-aloud features depend on the browser. Chrome on Android is the recommended demo browser.

## 3. Live personal-number call routing

Do not enter your personal phone number in source code or commit it to git. A cellular number cannot be answered by a local program on its own. Calls must first reach an Exotel virtual number; you configure your SIM/carrier to forward unanswered or all calls to that virtual number.

```
caller → your mobile number → carrier forwarding → Exotel virtual number
       → Exotel Voicebot (WSS) → Sahakar AI backend
       → Google Speech-to-Text → Gemini 3.7 Flash → Google Text-to-Speech
       → Exotel → caller hears the answer
```

### Provider requirements

- An Exotel account with KYC and an active virtual number (ExoPhone).
- Exotel’s **Voicebot Applet** enabled for the account. Ask Exotel support if it does not appear in App Bazaar.
- A public HTTPS/WSS deployment of this backend with a valid TLS certificate. `localhost` cannot receive Exotel calls.
- A Google Cloud project with Speech-to-Text and Text-to-Speech enabled, plus a service-account key available only on the server.

### Environment for live calls

On the deployed server, set these extra values in `backend/.env`:

```env
GOOGLE_APPLICATION_CREDENTIALS=/secure/path/service-account.json
EXOTEL_STREAM_TOKEN=use-a-long-random-secret
```

Never commit the service-account file or this `.env` file.

### Exotel call flow

1. Buy/activate an ExoPhone and complete KYC in Exotel.
2. In **App Bazaar**, create a Custom App and add a **Voicebot** applet as the first call-handling step.
3. Configure its WebSocket URL as:

   ```text
   wss://YOUR_PUBLIC_DOMAIN/api/phone/stream?token=YOUR_EXOTEL_STREAM_TOKEN
   ```

   Select 8 kHz (or leave the default) and enable bidirectional voice streaming. Do not enable recording unless callers are clearly notified and you have a legal retention policy.
4. Add a Passthru/Hangup step after the Voicebot applet, save the flow, then attach it to the ExoPhone.
5. In your mobile carrier settings, enable call forwarding from your own personal number to the ExoPhone. This is a carrier setting and may incur forwarding charges; test it with a second phone.
6. Call your personal number. The automated greeting should answer through Exotel and you can speak normally. Pause briefly after each question so the system can detect the end of the utterance.

The backend endpoint implements Exotel Voicebot events (`start`, `media`, `clear`, `stop`) and returns correctly formatted 16-bit mono PCM audio. It deliberately validates an optional token and never contains the personal number.

## Security checklist

- `backend/.env`, Google service-account JSON, and provider tokens are excluded by `.gitignore`.
- The frontend has no Gemini or telephony secrets.
- Set `CORS_ORIGIN` to the deployed frontend URL before production use.
- Use `wss://` with a valid certificate for Exotel; do not expose the backend with an unauthenticated public WebSocket.
