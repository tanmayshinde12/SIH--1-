# MASTER BUILD PROMPT — SIH 26088 MULTILINGUAL COOPERATIVE AI

You are the primary coding agent for my SIH 26088 prototype:

**Problem:** Multilingual Cooperative Governance & Legal Assistance Chatbot
**Organization:** Ministry of Cooperation
**Department:** National Council for Cooperative Training (NCCT)

## 🚨 CRITICAL: FOLLOW THIS PLAN STRICTLY

We have only approximately **2–3 hours for implementation**.

Do NOT over-engineer this project.

Do NOT build our own LLM inference system.

Do NOT build a custom real-time voice pipeline.

Do NOT build a complicated vector database/RAG architecture unless absolutely necessary.

Do NOT create an Android native application.

Do NOT spend time on unnecessary animations, authentication, dashboards, admin panels, microservices, Kubernetes, Docker complexity, or production infrastructure.

The goal is a **beautiful, convincing, functional SIH prototype** that demonstrates the complete concept.

Prioritize:

1. Working AI chat
2. Multilingual input/output
3. Voice-to-text input
4. Text-to-speech playback
5. File upload
6. Gemini API integration
7. Clean responsive UI for laptop + Android browser
8. Secure `.env` API-key handling
9. Optional telephone integration as the FINAL phase

---

# 1. CORE ARCHITECTURE

Build ONE backend API that acts as the central AI service.

The architecture should be:

USER
↓
React Web UI
↓
Our Backend API
↓
Gemini 3.7 Flash
↓
Response
↓
React Web UI

The same backend must eventually support:

* Web application
* Android browser
* Kiosk
* Telephone/voice gateway

Do NOT create separate AI logic for each interface.

The backend is the **single source of truth for AI behavior**.

---

# 2. GEMINI MODEL

Use:

`gemini-3.7-flash`

Google currently lists Gemini 3.7 Flash as the latest stable Flash model.

Use the official Google Gemini SDK/API.

For a new implementation, prefer the current Gemini **Interactions API** if the SDK makes it straightforward.

If the current SDK/API causes unnecessary implementation complexity or compatibility problems, use the simplest officially supported Gemini API path that works reliably.

Do NOT spend more than a few minutes fighting an SDK version.

The model must be configurable through an environment variable:

`GEMINI_MODEL=gemini-3.7-flash`

Never hard-code the API key.

---

# 3. API KEY SECURITY

Create a backend `.env` file:

```env
GEMINI_API_KEY=YOUR_KEY_HERE
GEMINI_MODEL=gemini-3.7-flash
```

Add `.env` to `.gitignore`.

The frontend MUST NEVER contain the Gemini API key.

Correct architecture:

Browser
↓
Our backend
↓
Gemini API

NOT:

Browser
↓
Gemini API directly

The frontend communicates only with our backend.

Also create:

`.env.example`

containing:

```env
GEMINI_API_KEY=
GEMINI_MODEL=gemini-3.7-flash
```

Do not put a real API key in `.env.example`.

---

# 4. BACKEND

Use whichever backend framework is fastest and most reliable.

Preferred:

**Python + FastAPI**

Create a clean backend such as:

```text
backend/
├── app/
│   ├── main.py
│   ├── config.py
│   ├── gemini.py
│   ├── routes/
│   │   ├── chat.py
│   │   ├── upload.py
│   │   └── voice.py
│   └── services/
│       └── ai_service.py
├── .env
├── .env.example
├── .gitignore
├── requirements.txt
└── README.md
```

Keep the structure simple.

---

# 5. PRIMARY CHAT API

Create:

`POST /api/chat`

Input:

```json
{
  "message": "user question",
  "conversation_id": "optional-id"
}
```

Output:

```json
{
  "answer": "AI response",
  "language": "mr",
  "model": "gemini-3.7-flash"
}
```

The backend should send the user's message directly to Gemini.

Do NOT translate Marathi → English → Gemini → Marathi.

Do NOT translate Hindi → English → Gemini → Hindi.

Do NOT create an unnecessary translation layer.

Gemini should directly understand the user's language and answer in the same language.

---

# 6. MULTILINGUAL BEHAVIOR

The system must support at minimum:

* Marathi
* Hindi
* English

The user can write or speak naturally.

Examples:

```text
"PMFBY मध्ये पीक विमा कसा मिळतो?"
```

Expected:

Marathi response.

---

```text
"PMFBY में फसल बीमा कैसे मिलता है?"
```

Expected:

Hindi response.

---

```text
"How can I claim crop insurance?"
```

Expected:

English response.

---

## SYSTEM INSTRUCTION FOR GEMINI

Use a strong system instruction similar to:

"You are Sahakar AI, a multilingual cooperative governance and rural assistance assistant.

Answer the user in the same language they use unless they explicitly request another language.

Support Marathi, Hindi, English and other languages that you understand.

Explain cooperative laws, government schemes, PACS services, PMFBY, financial literacy and grievance procedures in simple language suitable for rural users.

Do not invent laws, government schemes, deadlines, eligibility requirements or official procedures.

When information may be time-sensitive, prefer current verified information when the configured Gemini tools/search capabilities are available.

For legal matters, provide informational guidance rather than claiming to be a lawyer or giving definitive legal representation.

If you are uncertain, clearly say that the user should verify the information with the relevant official authority.

Use simple, practical explanations.

Do not unnecessarily translate the user's language into another language."

---

# 7. FRONTEND

Use:

**React + Vite**

unless another existing project structure is already working.

Do NOT create a native Android application.

The React website must be fully responsive.

It must work on:

* Laptop
* Desktop
* Android mobile browser

Design it like a modern Indian government/service AI assistant.

Project branding:

**Sahakar AI**

Subtitle:

**Multilingual Cooperative Governance & Legal Assistance Assistant**

---

# 8. FRONTEND UI

Create a polished interface.

Do NOT make a generic developer dashboard.

The main screen should look like an AI assistant.

Suggested structure:

```text
┌──────────────────────────────────────────────┐
│ 🌾 Sahakar AI                         ☰      │
│ Cooperative AI Assistant                    │
├──────────────────────────────────────────────┤
│                                              │
│       नमस्कार! 🙏                            │
│                                              │
│  मी सहकारी संस्था, सरकारी योजना, PMFBY,     │
│  PACS आणि तक्रार निवारणाबद्दल मदत करू शकतो. │
│                                              │
│              [Start asking]                  │
│                                              │
│──────────────────────────────────────────────│
│                                              │
│              Chat messages                   │
│                                              │
│                                              │
├──────────────────────────────────────────────┤
│ 📎   🎤   Type your question...       ➤     │
└──────────────────────────────────────────────┘
```

Include:

* Chat history
* User messages
* AI messages
* Loading indicator
* Error state
* File upload
* Microphone button
* Send button
* Speaker/read-aloud button
* Language indicator
* Clear/new conversation option

---

# 9. VOICE INPUT — IMPORTANT

We are NOT building real-time voice conversation.

Do NOT implement:

microphone
→ streaming audio
→ streaming STT
→ streaming LLM
→ streaming TTS

That is NOT required for this prototype.

Instead implement:

### PUSH → SPEAK → STOP → TRANSCRIBE → SUBMIT

Flow:

```text
User clicks 🎤
↓
Browser microphone starts
↓
User speaks
↓
User stops
↓
Speech is converted to text
↓
Text appears in input box
↓
User presses Send
↓
Backend receives text
↓
Gemini answers
```

Use the browser's available Speech Recognition API if supported.

If browser speech recognition is unavailable, gracefully show:

"Voice input is not supported in this browser."

Do not spend significant time building a custom streaming STT system.

---

# 10. TEXT-TO-SPEECH — IMPORTANT

We specifically DO NOT want real-time TTS.

The response must first appear as text.

Example:

```text
User asks question
↓
Gemini response
↓
Display complete response
↓
🔊 "Listen" button
↓
Browser reads the response
```

Use browser-native speech synthesis if practical.

Example conceptual implementation:

```javascript
window.speechSynthesis
```

Detect the response language if possible and select an appropriate language voice:

* Marathi: `mr-IN`
* Hindi: `hi-IN`
* English: `en-IN`

If a specific voice is unavailable, fall back gracefully.

Do NOT implement token-by-token speech.

Do NOT stream TTS.

Do NOT make the AI talk while the text is still generating.

The user explicitly wants:

**TEXT FIRST → THEN PLAY VOICE**

---

# 11. FILE UPLOAD

Add an upload button:

`📎 Upload document`

The prototype should support common formats such as:

* PDF
* image
* TXT

For the first implementation, keep file processing simple.

Preferred flow:

```text
User uploads document
↓
Backend receives file
↓
Backend sends supported file content to Gemini
↓
User asks question
↓
Gemini answers based on document
```

If direct Gemini file handling is easier, use it.

Do NOT spend hours implementing a complete embedding/vector database system.

The objective is:

**"User can upload a government/legal document and ask questions about it."**

---

# 12. OPTIONAL CURRENT-INFORMATION / WEB SEARCH

If Gemini's current API and selected model support Google Search grounding/tools cleanly, enable it.

However:

THIS IS SECONDARY TO THE BASIC CHAT.

Priority order:

1. Chat works
2. Multilingual works
3. Voice input works
4. TTS playback works
5. File upload works
6. Search grounding
7. Phone integration

If search grounding causes problems, disable it temporarily.

Do not break the main application for this feature.

---

# 13. CONVERSATION MEMORY

Maintain basic conversation history.

The UI should show:

```text
User
AI
User
AI
User
AI
```

Send sufficient recent conversation context to Gemini so follow-up questions work.

Example:

User:
"PMFBY म्हणजे काय?"

AI:
answer

User:
"त्यासाठी कोण पात्र आहे?"

Gemini should understand that "त्यासाठी" refers to PMFBY.

Keep the implementation simple.

---

# 14. ERROR HANDLING

The backend must gracefully handle:

* Missing API key
* Gemini API failure
* Rate limit
* Empty message
* File upload failure
* Network failure

Never expose:

* API keys
* stack traces
* internal server errors

to the frontend.

Show friendly messages.

---

# 15. API DOCUMENTATION

Create a simple README explaining:

```text
1. Install dependencies
2. Create .env
3. Add GEMINI_API_KEY
4. Start backend
5. Start frontend
6. Open browser
```

Include exact commands.

Example:

```bash
cd backend
pip install -r requirements.txt
uvicorn app.main:app --reload
```

and:

```bash
cd frontend
npm install
npm run dev
```

Adapt commands to the actual project.

---

# 16. PHONE INTEGRATION — LAST PRIORITY

ONLY work on phone integration after the web application is working.

Do NOT allow phone integration to delay the web prototype.

The target architecture is:

```text
Caller
↓
Exotel Trial Number
↓
Exotel Call Flow
↓
Your Backend
↓
Speech-to-Text
↓
Gemini
↓
Text-to-Speech
↓
Exotel
↓
Caller
```

We are NOT promising a production toll-free number for tomorrow.

The prototype goal is simply:

**A temporary/provider-provided phone number that can receive a call and connect it to the AI backend.**

---

# 17. EXOTEL SETUP GUIDE

If there is sufficient time, create a detailed `PHONE_SETUP.md`.

It must guide me step-by-step as a non-expert.

Include:

### Step 1

Create/login to Exotel.

### Step 2

Create the trial account.

### Step 3

Obtain the trial phone number.

### Step 4

Explain exactly where in the Exotel dashboard I should click.

### Step 5

Explain how to create/configure the call flow.

### Step 6

Explain how the call is connected to our server.

### Step 7

Explain what public URL/WebSocket endpoint our backend needs.

### Step 8

Explain how to test the call.

### Step 9

Explain common errors.

Do not assume I already understand telephony.

Use screenshots only if they can be generated automatically or if official documentation provides them.

Do NOT invent dashboard button names if the current Exotel interface differs.

When giving setup instructions, reference official Exotel documentation.

---

# 18. PHONE BACKEND ENDPOINT

Prepare a clean abstraction for phone integration.

For example:

```text
POST /api/phone/incoming
```

and/or:

```text
WS /api/phone/stream
```

depending on the actual Exotel integration mechanism.

Do NOT pretend that a normal HTTP endpoint can automatically become a bidirectional telephone audio stream.

Use the actual provider mechanism.

If phone integration cannot be completed in the available time, leave the endpoint/interface documented and keep the web application fully functional.

---

# 19. KIOSK ARCHITECTURE

Do NOT build physical hardware now.

The kiosk is a future client of the same backend.

Architecture:

```text
Kiosk
├── Microphone
├── Speaker
├── Touchscreen
└── Internet
       ↓
   Sahakar AI API
       ↓
    Gemini
```

The kiosk must NOT have separate AI logic.

It uses the same backend as the website.

Add a short `KIOSK_ARCHITECTURE.md` explaining this.

---

# 20. IMPORTANT MODEL-ABSTRACTION DESIGN

Although the prototype uses Gemini, structure the backend so that our own future 2B model can replace Gemini.

Create an abstraction such as:

```text
AIProvider
   │
   ├── GeminiProvider
   │
   └── LocalLLMProvider (future)
```

For tomorrow:

```text
AIProvider = GeminiProvider
```

Future:

```text
AIProvider = LocalLLMProvider
```

DO NOT implement the local LLM provider now unless it already exists.

This is an architectural extension point only.

---

# 21. DO NOT CALL THIS A FAKE SYSTEM

The implementation is a legitimate prototype.

Use this architecture:

```text
Prototype:
Gemini 3.7 Flash API

Production:
Our domain-specific 2B model
```

The frontend and backend remain the same.

Only the model provider changes.

---

# 22. PRIORITY MATRIX

If time becomes limited, follow this exact priority:

### P0 — MUST WORK

* [ ] React UI
* [ ] Backend
* [ ] Gemini API
* [ ] `.env`
* [ ] Text chat
* [ ] Marathi
* [ ] Hindi
* [ ] English
* [ ] Responsive Android browser UI

### P1 — SHOULD WORK

* [ ] Microphone → text
* [ ] Text → Gemini
* [ ] AI response displayed
* [ ] Listen button
* [ ] Browser TTS
* [ ] File upload

### P2 — NICE TO HAVE

* [ ] Google Search grounding
* [ ] Better conversation memory
* [ ] Source display
* [ ] Better document handling

### P3 — LAST

* [ ] Exotel
* [ ] Phone number
* [ ] Phone STT
* [ ] Phone TTS

If P0 is not finished, STOP working on P3.

---

# 23. DEMO SCENARIO

The application must be optimized for this demonstration:

### Demo 1 — Marathi

User clicks microphone.

Says:

"PMFBY मध्ये पीक विम्याचा दावा कसा करायचा?"

Voice input becomes text.

User clicks Send.

Gemini responds in Marathi.

The response appears on screen.

User clicks:

🔊 Listen

Browser reads the Marathi answer.

---

### Demo 2 — Hindi

User asks:

"सहकारी समिति में सदस्य के अधिकार क्या हैं?"

AI responds in Hindi.

---

### Demo 3 — English

User asks:

"What services can a PACS provide?"

AI responds in English.

---

### Demo 4 — Document

Upload a cooperative/government PDF.

Ask:

"इस दस्तावेज़ में सदस्य के लिए क्या प्रावधान हैं?"

AI answers based on the document.

---

### Demo 5 — Current information

If search grounding is enabled:

Ask a current scheme-related question.

Show that the system can retrieve current information instead of relying solely on static model knowledge.

---

### Demo 6 — Phone

If Exotel integration is successfully completed:

Call the trial number.

Demonstrate:

Caller
→ AI greeting
→ caller speaks
→ backend processes request
→ Gemini generates answer
→ AI responds.

If this is not completed, do NOT break the web demo.

---

# 24. VISUAL DESIGN

The UI should feel like a real Indian rural/government AI service.

Use:

* clean typography
* accessible contrast
* large buttons
* large microphone button
* clear language indicator
* simple icons
* mobile-first responsive layout
* subtle agricultural/cooperative visual identity

Avoid:

* excessive gradients
* excessive animations
* flashy gaming UI
* developer-looking interfaces
* unnecessary dashboards

The most important interaction should be:

**SPEAK → SEND → READ/LISTEN**

---

# 25. DEVELOPMENT STRATEGY

Before writing lots of code:

1. Inspect the existing repository.
2. Determine whether frontend/backend already exist.
3. Reuse existing working code.
4. Do NOT rewrite working components unnecessarily.
5. Install only required dependencies.
6. Implement backend Gemini call.
7. Test backend.
8. Implement frontend.
9. Test frontend.
10. Add voice.
11. Add file upload.
12. Attempt phone integration last.

After each major step, TEST it.

Do not build the entire system and test only at the end.

---

# 26. FINAL SECURITY CHECK

Before declaring completion:

Confirm:

* `.env` exists locally
* `.env` is in `.gitignore`
* Gemini key is not in frontend code
* Gemini key is not hard-coded
* Gemini key is not printed in logs
* `.env.example` contains no secret
* frontend communicates with backend
* backend communicates with Gemini

---

# 27. FINAL DELIVERABLE

At completion, provide:

```text
PROJECT
├── frontend/
├── backend/
├── .env.example
├── README.md
├── PHONE_SETUP.md
└── KIOSK_ARCHITECTURE.md
```

Also provide me with:

1. Exact commands to run backend.
2. Exact commands to run frontend.
3. Exact URL to open locally.
4. Exact environment variables required.
5. Gemini API setup steps.
6. Voice testing instructions.
7. File-upload testing instructions.
8. Exotel setup instructions if implemented.
9. List of what is fully working.
10. List of what is intentionally future scope.

---

# 🚨 FINAL INSTRUCTION

You are operating under a severe time constraint.

**DO NOT OVERBUILD.**

A working simple system is infinitely more valuable for tomorrow's prototype than an ambitious incomplete system.

The absolute minimum successful product is:

```text
Responsive React UI
        ↓
Microphone / Text
        ↓
Backend API
        ↓
Gemini 3.7 Flash
        ↓
Same-language answer
        ↓
Display answer
        ↓
🔊 Listen button
```

Then add:

```text
File Upload
```

Then, ONLY IF TIME REMAINS:

```text
Exotel Trial Phone
```

Keep the architecture clean enough that our own 2B model can replace Gemini later.

Start by inspecting the repository and determining the fastest path to P0 completion. Do not ask unnecessary questions. Make reasonable implementation decisions yourself and keep moving.
